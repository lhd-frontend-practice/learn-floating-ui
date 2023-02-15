<script setup lang="ts">
import { onMounted } from "vue";
import { computePosition, flip, shift, offset, arrow } from "@floating-ui/dom";

onMounted(() => {
  const button = document.querySelector("#button") as HTMLButtonElement;
  const tooltip = document.querySelector("#tooltip") as HTMLDivElement;
  const arrowElement = document.querySelector("#arrow") as HTMLDivElement;

  function update() {
    if (button && tooltip) {
      computePosition(button, tooltip, {
        placement: "right",
        middleware: [
          offset(6),
          flip(),
          shift({ padding: 5 }),
          arrow({ element: arrowElement }),
        ],
      }).then(({ x, y, middlewareData, placement }) => {
        Object.assign(tooltip.style, {
          left: `${x}px`,
          top: `${y}px`,
        });

        if (middlewareData.arrow) {
          const { x: arrowX, y: arrowY } = middlewareData.arrow;

          const staticSide = {
            top: "bottom",
            right: "left",
            bottom: "top",
            left: "right",
          }[placement.split("-")[0]] as "top" | "right" | "bottom" | "left";

          Object.assign(arrowElement.style, {
            left: arrowX != null ? `${arrowX}px` : "",
            top: arrowY != null ? `${arrowY}px` : "",
            right: "",
            bottom: "",
            [staticSide]: "-4px",
          });
        }
      });
    }
  }

  function showTooltip() {
    tooltip.style.display = "block";
    update();
  }

  function hideTooltip() {
    tooltip.style.display = "";
  }

  button.addEventListener("mouseenter", showTooltip);
  button.addEventListener("mouseleave", hideTooltip);
  button.addEventListener("focus", showTooltip);
  button.addEventListener("blur", hideTooltip);

  // [
  //   ["mouseenter", showTooltip],
  //   ["mouseleave", hideTooltip],
  //   ["focus", showTooltip],
  //   ["blur", hideTooltip],
  // ].forEach(([event, listener]) => {
  //   button.addEventListener(event, listener);
  // });
});
</script>

<template>
  <div class="container">
    <div class="inner-container">
      <button id="button" aria-describedby="tooltip">My button</button>
      <div id="tooltip" role="tooltip">
        My tooltip with more content
        <div id="arrow"></div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.container {
  height: 300px;
  overflow: scroll;
}

.inner-container {
  height: 800px;
}

#button {
  margin: 90px;
  border: none;
  padding: 10px 20px;
  border-radius: 4px;
}

#tooltip {
  display: none;
  width: max-content;
  position: absolute;
  top: 0;
  left: 0;
  background: #222;
  color: white;
  font-weight: bold;
  padding: 5px;
  border-radius: 4px;
  font-size: 90%;
}

#arrow {
  position: absolute;
  background: #222;
  width: 8px;
  height: 8px;
  transform: rotate(45deg);
}
</style>
