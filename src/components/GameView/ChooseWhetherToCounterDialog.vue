<template>
  <BaseDialog id="counter-dialog" v-model="show" title="Chance to Counter">
    <template #body>
      <div v-if="!opponentLastTwo" class="my-2">
        Your opponent has played the 
        <GameCardName :card-name="oneOff.name" />
        as a one-off
        <span v-if="target"> 
          targeting your 
          <GameCardName :card-name="target.name" />
        </span>
      </div>
      <div v-else class="my-2">
        Your opponent has played 
        <GameCardName :card-name="opponentLastTwo.name" />

        to Counter
        <span v-if="playerLastTwo">
          your 
          <GameCardName :card-name="playerLastTwo.name" />
        </span>
      </div>
      <div class="d-flex justify-center align-center my-8">
        <GameCard :suit="oneOff.suit" :rank="oneOff.rank" />
        <p class="ml-8">
          {{ t(`game.moves.effects[${oneOff.rank}]`) }}
        </p>
        <div v-if="target" id="target-wrapper">
          <span id="target-icon-wrapper" class="d-flex justify-center align-center">
            <v-icon
              size="x-large"
              color="red"
              icon="mdi-target" 
              aria-hidden="true"           
            />
          </span>
          <GameCard :suit="target.suit" :rank="target.rank" />
        </div>
      </div>
      Would you like to play a two to counter?
    </template>

    <template #actions>
      <v-btn
        data-cy="decline-counter-resolve"
        color="surface-1"
        variant="outlined"
        class="mr-4"
        @click="resolve"
      >
        Resolve
      </v-btn>
      <v-btn
        data-cy="counter"
        color="surface-1"
        variant="flat"
        @click="$emit('choose-to-counter')"
      >
        Counter
      </v-btn>
    </template>
  </BaseDialog>
</template>

<script>
import { useI18n } from 'vue-i18n';
import BaseDialog from '@/components/Global/BaseDialog.vue';
import GameCard from '@/components/GameView/GameCard.vue';
import GameCardName from '@/components/GameView/GameCardName.vue';

export default {
  name: 'ChooseWhetherToCounterDialog',
  components: {
    BaseDialog,
    GameCard,
    GameCardName,
  },
  props: {
    modelValue: {
      type: Boolean,
      required: true,
    },
    oneOff: {
      type: Object,
      required: true,
    },
    target: {
      type: Object,
      default: null,
    },
    twosPlayed: {
      type: Array,
      required: true,
    },
  },
  emits: ['choose-to-counter', 'resolve'],
  setup() {
    const { t } = useI18n();
    return { t };
  },
  computed: {
    show: {
      get() {
        return this.modelValue;
      },
      set() {
        // do nothing - parent controls whether dialog is open
      },
    },
    thereAreTwos() {
      return this.twosPlayed && this.twosPlayed.length > 0;
    },
    opponentLastTwo() {
      return this.thereAreTwos
        ? this.twosPlayed[this.twosPlayed.length - 1]
        : null;
    },
    playerLastTwo() {
      return this.thereAreTwos
        ? this.twosPlayed[this.twosPlayed.length - 2]
        : null;
    },
  },
  methods: {
    resolve() {
      this.$emit('resolve');
    },
  },
};
</script>

<style lang="scss" scoped>
#target-wrapper {
  position: relative;
  & #target-icon-wrapper {
    position: absolute;
    width: 100%;
    height: 100%;
    z-index: 1;
  }
}
</style>
