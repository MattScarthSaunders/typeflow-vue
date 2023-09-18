<template>
  <div ref="wrapper" @vnodeUpdated="handleSlotChange">
    <slot></slot>
  </div>
</template>

<script setup lang="ts">
/**
 * `TypeFlow` is a Vue component that progressively reveals its child text content,
 * simulating a typewriter effect. By default, characters are rendered sequentially
 * with a slightly variable delay between each character, but with longer charDelays for end-of-sentence char and commas.
 *
 * @param {number} props.charDelay The initial delay that the typing occurs. Default delay is 50ms.
 * @param {characterMultipliers} props.characterMultipliers Delay multiplier for a given character.
 *
 * @example
 * ```jsx
 * <TypeFlow
 *    charDelay={50}
 *    characterMultipliers={{
 *      '?': number;
 *      '!': number;
 *      '.': number;
 *      ',': number;
 * }}>
 *   <p>hello, world.</p>
 * </TypeFlow>
 * ```
 */

import { ref, onMounted } from 'vue';
import { CharacterMultipliers } from '../../../declaration/src/types';

const props = defineProps<{
  charDelay?: number;
  characterMultipliers?: CharacterMultipliers;
}>();

const wrapper = ref<HTMLElement | null>(null);
const isTyping = ref(false);
const content = ref('');
const text = ref('');
const slotElement = ref<Element | undefined>(document.createElement('p'));
const stopTyping = ref(false);
const delay = (ms: number) => new Promise((resolve) => setTimeout(resolve, ms));

onMounted(async () => {
  updateSlotContent();
  typeCharacter();
});

function updateSlotContent() {
  content.value = '';
  slotElement.value = wrapper.value?.children[0];
  text.value = slotElement.value?.innerHTML ?? '';
}

const typeCharacter = async () => {
  isTyping.value = true;
  if (wrapper.value) wrapper.value.style.visibility = 'visible';

  for (let i = 0; i < text.value.length; i++) {
    if (stopTyping.value) {
      stopTyping.value = false;
      break;
    }
    content.value = content.value + text.value[i];

    if (slotElement.value) slotElement.value.innerHTML = content.value;

    const time = getcharDelayForCharacter(
      text.value[i],
      props.charDelay,
      props.characterMultipliers
    );
    await delay(time);
  }
  isTyping.value = false;
};

function getcharDelayForCharacter(
  char: string,
  charDelay: number = 50,
  charsTocharDelay: CharacterMultipliers = {
    '?': 8,
    '!': 8,
    '.': 6,
    ',': 4
  }
): number {
  const betweenOneAndTwo = Math.random() + 1;
  let randomisedCharDelay = (charsTocharDelay[char] || 1) * betweenOneAndTwo;

  if (char === ' ') {
    const zeroToThreeIsh = (Math.random() * 10) / 3;
    randomisedCharDelay = (charsTocharDelay[' '] || zeroToThreeIsh) * betweenOneAndTwo;
  }

  return charDelay * randomisedCharDelay;
}

function handleSlotChange() {
  if (isTyping.value) stopTyping.value = true;
  if (wrapper.value) wrapper.value.style.visibility = 'hidden';

  // this is necessary to avoid jumbled text
  setTimeout(() => {
    slotElement.value = undefined;
    content.value = '';
    text.value = '';
    updateSlotContent();
    typeCharacter();
  }, 500);
}
</script>

<style scoped>
div {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
</style>
