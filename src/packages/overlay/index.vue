<template>
  <Transition name="overlay-fade">
    <view
      :class="classes"
      @touchmove.stop="touchmove"
      @click="onClick"
      :style="style"
      v-show="visible"
    >
      <slot></slot>
    </view>
  </Transition>
</template>
<script lang="ts">
import { CSSProperties, PropType, computed } from 'vue';
import { createComponent } from '@/utils/create';
const { componentName, create } = createComponent('overlay');
const overlayProps = {
  visible: {
    type: Boolean,
    default: false
  },
  zIndex: {
    type: [Number, String],
    default: 2000
  },
  duration: {
    type: [Number, String],
    default: 0.3
  },
  overlayClass: {
    type: String,
    default: ''
  },
  lockScroll: {
    type: Boolean,
    default: true
  },
  overlayStyle: {
    type: Object as PropType<CSSProperties>
  },
  closeOnClickOverlay: {
    type: Boolean,
    default: true
  }
};

export { overlayProps };

export default create({
  props: overlayProps,
  emits: ['click', 'update:visible'],
  setup(props, { emit }) {
    const classes = computed(() => {
      const prefixCls = componentName;
      return {
        [prefixCls]: true,
        [props.overlayClass]: true
      };
    });

    const style = computed(() => {
      return {
        animationDuration: `${props.duration}s`,
        zIndex: props.zIndex,
        ...props.overlayStyle
      };
    });

    const touchmove = (e: TouchEvent) => {
      if (props.lockScroll) e.preventDefault();
    };

    const onClick = (e: MouseEvent) => {
      emit('click', e);
      if (props.closeOnClickOverlay) {
        emit('update:visible', false);
      }
    };

    return { classes, style, touchmove, onClick };
  }
});
</script>
<style lang="scss">
@import 'index.scss';
</style>
