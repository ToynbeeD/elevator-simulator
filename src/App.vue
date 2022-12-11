<template>
  <div class="container">
    <div class="floor" v-for="floor in floorsCount" :key="floor" ref="floors">
      <div class="floor-shaft" v-for="shaft in elevatorsCount" :key="shaft">
        <div
          ref="elevator"
          class="elevator"
          :style="{ bottom: elevatorPositionBottom + 'px' }"
          v-if="floor === currentFloor"
        ></div>
      </div>
      <button
        class="floor-button"
        :disabled="isElevatorActive"
        @click="moveElevator(floor)"
      >
        Вызвать
      </button>
    </div>
  </div>
</template>

<script setup>
import { ref } from "vue";

const floorsCount = 5;
const elevatorsCount = 1;

const floors = ref([]);
const currentFloor = ref(1);
const elevator = ref([]);
const elevatorPositionBottom = ref(0);
const isElevatorActive = ref(false);

async function moveElevator(targetFloor) {
  if (currentFloor.value === targetFloor) {
    return;
  }

  const isMovingUp = currentFloor.value < targetFloor;

  isElevatorActive.value = true;
  let checkElevatorPosition = false;

  while (!checkElevatorPosition) {
    await new Promise((resolve) => {
      setTimeout(() => {
        isMovingUp ? elevatorPositionBottom.value += 1 : elevatorPositionBottom.value -= 1;

        const elevatorCoords = elevator.value[0].getBoundingClientRect();
        const targetFloorCoords = floors.value[targetFloor - 1].getBoundingClientRect();

        checkElevatorPosition = elevatorCoords.y === targetFloorCoords.y;

        resolve();
      }, 5);
    });
  }

  currentFloor.value = targetFloor;
  elevatorPositionBottom.value = 0;
  isElevatorActive.value = false;
}
</script>

<style scoped>
.container {
  display: flex;
  flex-direction: column-reverse;
  width: 100%;
  height: 100%;
}

.floor {
  display: flex;
  align-items: center;
  padding-left: 20px;
  height: 200px;
  width: 100%;
  border-bottom: 1px solid #000;
}

.floor-shaft {
  position: relative;
  margin-right: 20px;
  height: 100%;
  width: 150px;
  border-right: 1px solid #000;
  border-left: 1px solid #000;
}

.elevator {
  position: absolute;
  left: 0;
  bottom: 0;
  width: 100%;
  height: 100%;
  background-color: navy;
}
</style>
