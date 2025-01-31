<template>
 <div
  :style="{ left: position.x + 'px', top: position.y + 'px', position: 'absolute' }"
  class="draggable"
  @mousedown="startDrag"
  @touchstart="startDrag"
  @dblclick="handleClick"
>
  <div class="blackbox">
    <img
      class="imageRef"
      ref="imageRef"
      v-if="imageSrc"
      :src="imageSrc"
      alt="Received Image"
    />
  </div>
</div>
</template>

<script>
/**
 * Vue component for displaying and dragging an image across the screen.
 * This component is used in an active game to show the still-active game stage.
 * 
 * @module DraggableImage
 * 
 * @vue-data {Array<number>} [position=[0,0]] - The current position of the image on the screen.
 * @vue-data {number} [position.x=0] - The x-coordinate of the image.
 * @vue-data {number} [position.y=0] - The y-coordinate of the image.
 * @vue-data {boolean} [dragging=false] - Indicates if the image is currently being dragged.
 * @vue-data {Array<number>} [offset=[0,0]] - The offset of the cursor from the image's top-left corner during dragging.
 * @vue-data {number} [offset.x=0] - The x-offset of the cursor.
 * @vue-data {number} [offset.y=0]- The y-offset of the cursor.
 * 
 * @vue-computed {Object} ...mapGetters - Vuex getters mapped to component computed properties.
 * @vue-computed {Object} enums - Provides enums imported from '../enums.js'.
 * 
 * @vue-event {MouseEvent} @mousedown - Initiates the drag operation.
 * @vue-event {MouseEvent} @dblclick - Returns to the PlayPage
 */
import { mapGetters } from "vuex";
import * as ENUMS from '../enums';
export default {
  data() {
    return {
      position: { x: 0, y: 0 },
      dragging: false,
      offset: { x: 0, y: 0 },
    };
  },
  computed: {
    ...mapGetters(["imageSrc","game"]),
    enums() { return ENUMS; }
  },
  methods: {
  /**
   * Starts the drag operation, calculates the offset, and adds event listeners for movement and release.
   * Works for both mouse and touch events.
   * @param {MouseEvent|TouchEvent} event 
   */
  startDrag(event) {
    this.dragging = true;
    const clientX = event.touches ? event.touches[0].clientX : event.clientX;
    const clientY = event.touches ? event.touches[0].clientY : event.clientY;
    this.offset.x = clientX - this.position.x;
    this.offset.y = clientY - this.position.y;

    if (event.type === 'mousedown') {
      document.addEventListener('mousemove', this.onDrag);
      document.addEventListener('mouseup', this.stopDrag);
    } else {
      document.addEventListener('touchmove', this.onDrag, { passive: false }); // Prevent default touch behavior
      document.addEventListener('touchend', this.stopDrag);
    }
  },
  /**
   * Updates the position of the image based on movement while dragging.
   * Prevents the page from scrolling during the drag.
   * Works for both mouse and touch events.
   * @param {MouseEvent|TouchEvent} event 
   */
  onDrag(event) {
    if (this.dragging) {
      event.preventDefault(); // Prevent scrolling
      const clientX = event.touches ? event.touches[0].clientX : event.clientX;
      const clientY = event.touches ? event.touches[0].clientY : event.clientY;
      this.position.x = clientX - this.offset.x;
      this.position.y = clientY - this.offset.y;
    }
  },
  /**
   * Stops the drag operation and removes event listeners.
   */
  stopDrag() {
    this.dragging = false;
    document.removeEventListener('mousemove', this.onDrag);
    document.removeEventListener('mouseup', this.stopDrag);
    document.removeEventListener('touchmove', this.onDrag);
    document.removeEventListener('touchend', this.stopDrag);
  },
  /**
   * Navigates to the 'play' route when the image is double-clicked.
   */
  handleClick() {
    this.$router.push({ name: 'play', params: { game: this.game }});
  },
}
};
</script>

<style scoped>
.draggable {
  cursor: grab;
  user-select: none;
  position: relative;
}

.draggable:active {
  cursor: grabbing;
}

.blackbox {
  width: 250px; /*TODO Change to Dynamic size */
  height: 250px; 
  background-color: black;
  position: relative;
  overflow: hidden;
}

.imageRef {
  width: 100%;
  height: 100%;
  object-fit: contain; 
  pointer-events: none; 
  border: 2px solid black; 
}
</style>
