<template>
  <v-list-item>
    <v-list-item-avatar size="32">
      <v-img
        :src="sender.thumb"
      />
    </v-list-item-avatar>

    <v-list-item-content>
      <v-list-item-title v-text="sender.username" />
      <template v-for="(msg, index) in processedMessages">
        <v-list-item-subtitle
          v-if="msg.text"
          class="message-content"
          v-text="msg.text"
          v-bind:key="index"
        />

        <v-img
          v-if="msg.image"
          :src="msg.image"
          v-bind:key="index"
        />
      </template>

    </v-list-item-content>
  </v-list-item>
</template>

<script>
import { mapGetters } from 'vuex';

export default {
  name: 'MessageItem',

  props: {
    message: {
      type: Object,
      required: true,
    },
  },

  computed: {
    ...mapGetters('synclounge', [
      'GET_MESSAGES_USER_CACHE_USER',
    ]),

    sender() {
      return this.GET_MESSAGES_USER_CACHE_USER(this.message.senderId);
    },

    processedMessages() {
      const regex = /\b(https?:\/\/\S+(?:png|jpe?g|gif)\S*)\b/gi; // find image URLs
      const parts = this.message.text.split(regex);
      return parts.map((str, index) => {
        if (index % 2 !== 0) {
          /* image URLs will be the odd numbered elements from the split */
          return { image: str, text: '' };
        }
        return { image: '', text: str };
      });
    },
  },
};
</script>

<style scoped>
.message-content {
  white-space: normal !important;
  font-weight: normal !important;
}
</style>
