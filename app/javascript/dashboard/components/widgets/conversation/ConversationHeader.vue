<template>
  <div class="conv-header">
    <div class="user">
      <Thumbnail
        :src="currentContact.thumbnail"
        size="40px"
        :badge="chatMetadata.channel"
        :username="currentContact.name"
        :status="currentContact.availability_status"
      />
      <div class="user--profile__meta">
        <h3 class="user--name text-truncate">
          {{ currentContact.name }}
        </h3>
        <woot-button
          class="user--profile__button"
          size="small"
          variant="link"
          @click="$emit('contact-panel-toggle')"
        >
          {{
            `${
              isContactPanelOpen
                ? $t('CONVERSATION.HEADER.CLOSE')
                : $t('CONVERSATION.HEADER.OPEN')
            } ${$t('CONVERSATION.HEADER.DETAILS')}`
          }}
        </woot-button>
      </div>
    </div>
    <div
      class="header-actions-wrap"
      :class="{ 'has-open-sidebar': isContactPanelOpen }"
    >
      <more-actions :conversation-id="currentChat.id" />
    </div>
  </div>
</template>
<script>
import { mapGetters } from 'vuex';
import MoreActions from './MoreActions';
import Thumbnail from '../Thumbnail';
import agentMixin from '../../../mixins/agentMixin.js';
import eventListenerMixins from 'shared/mixins/eventListenerMixins';
import { hasPressedAltAndOKey } from 'shared/helpers/KeyboardHelpers';

export default {
  components: {
    MoreActions,
    Thumbnail,
  },
  mixins: [agentMixin, eventListenerMixins],
  props: {
    chat: {
      type: Object,
      default: () => {},
    },
    isContactPanelOpen: {
      type: Boolean,
      default: false,
    },
  },

  data() {
    return {
      currentChatAssignee: null,
      inboxId: null,
    };
  },

  computed: {
    ...mapGetters({
      uiFlags: 'inboxAssignableAgents/getUIFlags',
      currentChat: 'getSelectedChat',
    }),

    chatMetadata() {
      return this.chat.meta;
    },

    currentContact() {
      return this.$store.getters['contacts/getContact'](
        this.chat.meta.sender.id
      );
    },
  },
  mounted() {
    const { inbox_id: inboxId } = this.chat;
    this.inboxId = inboxId;
  },

  methods: {
    handleKeyEvents(e) {
      if (hasPressedAltAndOKey(e)) {
        this.$emit('contact-panel-toggle');
      }
    },
  },
};
</script>

<style lang="scss" scoped>
.text-truncate {
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.conv-header {
  flex: 0 0 var(--space-jumbo);
}

.option__desc {
  display: flex;
  align-items: center;
}

.option__desc {
  &::v-deep .status-badge {
    margin-right: var(--space-small);
    min-width: 0;
    flex-shrink: 0;
  }
}
</style>
