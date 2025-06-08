<template>
  <div>
    <v-text-field
      v-model="messageToBeSent"
      append-outer-icon="send"
      :label="chatboxLabel"
      hide-details
      single-line
      class="ml-2 mr-2 pr-1"
      @click:append-outer="sendMessage"
      @keyup.enter.native="sendMessage"
    />
    <button
      type="button"
      class="emojibutton"
      @click="toggleHideEmoji"
      ref="emojiButton"
    >
      <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24">
        <!-- eslint-disable -->
        <path d="M12 0C5.373 0 0 5.373 0 12s5.373 12 12 12 12-5.373 12-12S18.627 0 12 0m0 22C6.486 22 2 17.514 2 12S6.486 2 12 2s10 4.486 10 10-4.486 10-10 10" />
        <path d="M8 7a2 2 0 1 0-.001 3.999A2 2 0 0 0 8 7M16 7a2 2 0 1 0-.001 3.999A2 2 0 0 0 16 7M15.232 15c-.693 1.195-1.87 2-3.349 2-1.477 0-2.655-.805-3.347-2H15m3-2H6a6 6 0 1 0 12 0" />
        <!-- eslint-enable -->
      </svg>
    </button>
    <div
      v-show="!hideEmojiPicker"
      ref="picker"
    />
  </div>
</template>

<script>

import { mapActions } from 'vuex';
import { data as emojiData } from '@emoji-mart/data';
import { Picker } from 'emoji-mart';

export default {
  name: 'MessageInput',

  data: () => ({
    messageToBeSent: '',
    hideEmojiPicker: true,
  }),

  computed: {
    chatboxLabel() {
      return 'Message';
    },
  },

  methods: {
    ...mapActions('synclounge', [
      'SEND_MESSAGE',
    ]),

    sendMessage() {
      if (this.messageToBeSent === '') {
        return;
      }
      console.log(`We should send this message: ${this.messageToBeSent}`);
      this.SEND_MESSAGE(this.messageToBeSent);
      this.messageToBeSent = '';
      this.hideEmojiPicker = true;
    },

    addCharacter(char) {
      this.messageToBeSent += char.native;
    },

    toggleHideEmoji() {
      this.hideEmojiPicker = !this.hideEmojiPicker;
    },

    hideEmoji(e) {
      console.log(e);
      if (!this.hideEmojiPicker && !this.$refs.emojiButton?.contains(e.target)) {
        this.hideEmojiPicker = true;
      }
    },
  },

  mounted() {
    const pickerOptions = {
      data: emojiData,
      onEmojiSelect: this.addCharacter,
      onClickOutside: this.hideEmoji,
    };
    const emojiPicker = new Picker(pickerOptions);
    this.$refs.picker.appendChild(emojiPicker);
  },
};
</script>

<style scoped>
.emojibutton {
  height: 2rem;
  width: 2rem;
  padding: 4px;
}
</style>
