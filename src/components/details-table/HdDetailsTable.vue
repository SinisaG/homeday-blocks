<template>
  <div class="details-table-wrap">
    <dl
      :class="{ withDivider }"
      class="details-table">
      <div
        v-if="$slots.before"
        class="details-table__before"
      >
        <slot name="before"/>
      </div>
      <div class="details-table__rows">
        <slot/>
      </div>
    </dl>
  </div>
</template>

<script>
export default {
  name: 'HdDetailsTable',
  props: {
    withDivider: {
      type: Boolean,
      default: true,
    },
  },
};
</script>

<style lang="scss">
@import 'homeday-blocks/src/styles/mixins.scss';

.details-table-wrap {
  padding-top: $stack-m;
  padding-bottom: $stack-s;

  + #{&} {
    padding-top: $stack-s;
  }
}

.details-table {
  &.withDivider {
    &::after {
      content: '';
      display: block;
      height: 2px;
      background-color: getShade($quaternary-color, 60);
      margin-top: $stack-s;
    }
  }

  &__before {

    @media (min-width: $break-desktop) {
      margin-bottom: $stack-s;
    }
  }

  &__rows {

    @media (min-width: $break-desktop) {
      display: flex;
      flex-flow: row wrap;
      align-items: flex-start;
    }
  }
}
</style>
