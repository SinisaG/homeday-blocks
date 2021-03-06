<template>
  <div class="notifications">
    <transition-group
      name="notifications-bar-"
      tag="div"
    >
      <HdNotificationsBar
        v-for="(notification, i) in notifications"
        ref="notifications"
        :key="notification.id || i"
        :visible="isLast(i)"
        :compact="compact"
        v-bind="notification"
      >
        <slot :notification="notification" />
      </HdNotificationsBar>
    </transition-group>
    <div
      ref="sizer"
      :style="{
        height: `${sizerHeight}px`,
      }"
      class="notifications__sizer"
    />
  </div>
</template>

<script>
import onResize from 'homeday-blocks/src/services/on-resize';
import HdNotificationsBar from 'homeday-blocks/src/components/notifications/HdNotificationsBar.vue';

export default {
  name: 'HdNotifications',
  components: {
    HdNotificationsBar,
  },
  props: {
    notifications: {
      type: Array,
      default: () => [],
    },
    compact: {
      type: Boolean,
      default: false,
    },
  },
  data() {
    return {
      sizerHeight: 0,
    };
  },
  watch: {
    notifications() {
      this.$nextTick(this.resizeNotifications);
    },
    sizerHeight() {
      this.$emit('heightChange', this.sizerHeight);
    },
  },
  mounted() {
    this.resizeNotifications();
    this.addResizeEvents();
  },
  beforeDestroy() {
    this.removeResizeEvents();
  },
  methods: {
    isLast(notificationIndex) {
      return notificationIndex === this.notifications.length - 1;
    },
    // Encapsulated in a method to be able to mock it in the tests
    getScrollHeight(el) {
      return el.scrollHeight;
    },
    resizeNotifications() {
      if (!this.notifications.length) {
        this.sizerHeight = 0;
        return;
      }

      if (!this.$refs.notifications) {
        this.sizerHeight = 0;
        return;
      }
      // Go through all the notifications and get the max height in order to
      // resize the sizer (since notification bars are position: fixed)
      const maxSize = this.$refs.notifications.reduce((maxHeight, { $el }) => {
        const elHeight = this.getScrollHeight($el);

        if (maxHeight >= elHeight) {
          return maxHeight;
        }

        return elHeight;
      }, 0);
      this.sizerHeight = maxSize;
    },
    resizeHandler() {
      this.resizeNotifications();
    },
    addResizeEvents() {
      onResize.onDebounced(this.resizeHandler);
    },
    removeResizeEvents() {
      onResize.offDebounced(this.resizeHandler);
    },
  },
};
</script>

<style lang="scss">
@import 'homeday-blocks/src/styles/mixins.scss';

.notifications {
  position: relative;

  &__sizer {
    transition: height ($time-s * 2) ease-in-out;
  }
}
</style>
