<template>
  <div>
    <div>
      <ComposerHeroPortrait v-for="hero in heroes" :key="hero.hero_id" :hero="hero" />
    </div>
    <div class="mt-5">
      <h3>Enemy Draft</h3>
      <div class="d-flex justify-content-around align-items-center">
        <div class="d-flex justify-content-around align-items-center nowrap w-50">
          <div class="hero-image" v-for="hero in enemyImage" :key="hero.id" :style="`background-image: url('${hero.img}'); background-size: cover;`"></div>
        </div>
      </div>
      <form @submit.prevent="composeHandler" v-if="!composedDraft.advantage">
        <input type="submit" value="Compose Draft" :class="`btn btn-primary btn-lg ${draftComposerEnemy.length === 5 && !isComposing ? '' : 'disabled'}`">
      </form>
      <div class="row mt-3" v-if="isComposing">
        <div class="col">
          <div class="spinner-border" role="status">
          </div>
        </div>
      </div>
    </div>
    <div v-if="composedDraft.advantage" class="mt-4">
      <h3>Your Draft</h3>
      <div class="d-flex justify-content-around align-items-center">
        <div class="d-flex justify-content-around align-items-center nowrap w-50">
            <div class="hero-image" v-for="hero in allyDraft" :key="hero.id" :style="`background-image: url('${hero.img}'); background-size: cover;`"></div>
        </div>
      </div>
      <div class="row mt-3">
        <div class="col"><h3>Effectiveness Against Enemy Draft</h3></div>
      </div>
      <div class="row">
        <div class="col"><h5>{{ composedDraft.advantage.toFixed(2) }}</h5></div>
      </div>
      <div class="row">
          <div class="offset-4 col-4">
            <h5>Carry</h5>
            <div class="progress">
              <div class="progress-bar" role="progressbar" :style="`width: ${composedDraft.allyComposition.Carry / 8 * 100}%;`" aria-valuenow="0" aria-valuemin="0" aria-valuemax="10">{{composedDraft.allyComposition.Carry}}</div>
            </div>
          </div>
        </div>
        <div class="row mt-2">
          <div class="offset-4 col-4">
            <h5>Escape</h5>
            <div class="progress">
              <div class="progress-bar" role="progressbar" :style="`width: ${composedDraft.allyComposition.Escape / 8 * 100}%;`" aria-valuenow="0" aria-valuemin="0" aria-valuemax="10">{{composedDraft.allyComposition.Escape}}</div>
            </div>
          </div>
        </div>
        <div class="row mt-2">
          <div class="offset-4 col-4">
            <h5>Nuker</h5>
            <div class="progress">
              <div class="progress-bar" role="progressbar" :style="`width: ${composedDraft.allyComposition.Nuker / 8 * 100}%;`" aria-valuenow="0" aria-valuemin="0" aria-valuemax="10">{{composedDraft.allyComposition.Nuker}}</div>
            </div>
          </div>
        </div>
        <div class="row mt-2">
          <div class="offset-4 col-4">
            <h5>Initiator</h5>
            <div class="progress">
              <div class="progress-bar" role="progressbar" :style="`width: ${composedDraft.allyComposition.Initiator / 8 * 100}%;`" aria-valuenow="0" aria-valuemin="0" aria-valuemax="10">{{composedDraft.allyComposition.Initiator}}</div>
            </div>
          </div>
        </div>
        <div class="row mt-2">
          <div class="offset-4 col-4">
            <h5>Durable</h5>
            <div class="progress">
              <div class="progress-bar" role="progressbar" :style="`width: ${composedDraft.allyComposition.Durable / 8 * 100}%;`" aria-valuenow="0" aria-valuemin="0" aria-valuemax="10">{{composedDraft.allyComposition.Durable}}</div>
            </div>
          </div>
        </div>
        <div class="row mt-2">
          <div class="offset-4 col-4">
            <h5>Disabler</h5>
            <div class="progress">
              <div class="progress-bar" role="progressbar" :style="`width: ${composedDraft.allyComposition.Disabler / 8 * 100}%;`" aria-valuenow="0" aria-valuemin="0" aria-valuemax="10">{{composedDraft.allyComposition.Disabler}}</div>
            </div>
          </div>
        </div>
        <div class="row mt-2">
          <div class="offset-4 col-4">
            <h5>Jungler</h5>
            <div class="progress">
              <div class="progress-bar" role="progressbar" :style="`width: ${composedDraft.allyComposition.Jungler / 8 * 100}%;`" aria-valuenow="0" aria-valuemin="0" aria-valuemax="10">{{composedDraft.allyComposition.Jungler}}</div>
            </div>
          </div>
        </div>
        <div class="row mt-2">
          <div class="offset-4 col-4">
            <h5>Support</h5>
            <div class="progress">
              <div class="progress-bar" role="progressbar" :style="`width: ${composedDraft.allyComposition.Support / 8 * 100}%;`" aria-valuenow="0" aria-valuemin="0" aria-valuemax="10">{{composedDraft.allyComposition.Support}}</div>
            </div>
          </div>
        </div>
        <div class="row mt-2">
          <div class="offset-4 col-4">
            <h5>Pusher</h5>
            <div class="progress">
              <div class="progress-bar" role="progressbar" :style="`width: ${composedDraft.allyComposition.Pusher / 8 * 100}%;`" aria-valuenow="0" aria-valuemin="0" aria-valuemax="10">{{composedDraft.allyComposition.Pusher}}</div>
            </div>
          </div>
        </div>
    </div>
  </div>
</template>

<script>
import { heroes } from 'dotaconstants';
import ComposerHeroPortrait from '@/components/ComposerHeroPortrait.vue';
import { mapState, mapActions } from 'vuex';

export default {
  name: 'DraftComposer',
  components: { ComposerHeroPortrait},
  data() {
    return {
      isComposing: false
    }
  },
  computed: {
    ...mapState(['draftComposerEnemy', 'composedDraft']),
    heroes() {
      return heroes;
    },
    enemyImage() {
      const enemyImageArr = [];
  
      for (let hero of this.draftComposerEnemy) {
        const payload = { id: hero, img: 'https://steamcdn-a.akamaihd.net/' + heroes[hero].img };

        enemyImageArr.push(payload);
      }

      return enemyImageArr;
    },
    allyDraft() {
      const allyImageArr = [];

      for (let role in this.composedDraft.allyDraft) {
        const payload = { 
          id: this.composedDraft.allyDraft[role].heroId2, 
          img: 'https://steamcdn-a.akamaihd.net/' + heroes[this.composedDraft.allyDraft[role].heroId2].img };

        allyImageArr.push(payload);
      }

      return allyImageArr;
    }
  },
  methods: {
    ...mapActions(['composeDraft']),
    async composeHandler() {
      this.isComposing = true;
      await this.composeDraft();
      this.isComposing = false;
    }
  }
}
</script>

<style scoped>
  .hero-image {
    height: 60px;
    width: 106px;
    background-color: #313131;
    border-radius: 4px;
    margin: 10px 0;
    box-sizing: content-box;
  }
</style>