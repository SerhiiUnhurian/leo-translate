<template>
  <div class="translate-list">
    <ul class="translate-list__list">
      <li
          v-for="trans in translationsWithRating" :key="trans.id"
          class="translate-list__item translate-list__item_meaning"
          title="Add this meaning to the dictionary."
          @click="$emit('add-meaning', trans.value)"
      >
        <div
            class="translate-list__rating"
            :style="{width: trans.rating+'%'}"
        ></div>
        <div class="translate-list__item-value">{{ trans.value }}</div>
      </li>
    </ul>

    <input
        class="translate-list__add-meaning"
        type="text"
        placeholder="Type meaning and press Enter"
        @keyup.enter="onEnterMeaningListener"
    >

    <div
        v-if="isLoading"
        class="translate-list__overlay"
    >
      <HollowDotsSpinner
          :animation-duration="500"
          :dot-size="23"
          :dots-num="5"
          color="#30e60b"
      />
    </div>
  </div>
</template>

<script>
  import HollowDotsSpinner from 'epic-spinners/src/components/lib/HollowDotsSpinner.vue';

  export default {
    components: {
      HollowDotsSpinner
    },

    props: {
      translations: {
        type: Array,
        required: true
      },
      isLoading: {
        type: Boolean,
        required: true
      }
    },

    computed: {
      translationsWithRating () {
        let max = 0;
        let min = 0;

        const translations = this.translations.map(item => {
          return typeof item.votes === 'undefined' ? Object.assign({}, item, { votes: item.vt }) : item;
        });

        translations.forEach(item => {
          if (item.votes > max) {
            max = item.votes;
          }
          if (item.votes < min || min === 0) {
            min = item.votes;
          }
        });

        return translations.map(item => {
          item.rating = (max === 0 && min === 0 || min >= max) ? 0 : 100 / (max - min) * (item.votes - min);

          return item;
        });
      }
    },

    methods: {
      onEnterMeaningListener (event) {
        this.$emit('add-meaning', event.target.value);
        event.target.value = '';
      }
    }
  };
</script>
