<template>
  <div>
    <h2>Authors starting with <span class="capitalised">{{$route.params.letter}}</span></h2>
    <h3>{{subtitle}}</h3>
    <author-list :data="results" :action="showTextsBy" />
    <el-pagination
      v-show="results.length !== 0"
      @current-change="goToPage"
      layout="prev, pager, next"
      :page-size="pagerLimit"
      :current-page.sync="page"
      :total="totalResults"
    />
  </div>
</template>

<script>
  import { mapState, mapGetters, mapActions, mapMutations } from 'vuex';
  import store from 'store/store';

  import authorList from 'components/authorList';

  export default {
    name: 'authors',
    components: {
      authorList,
    },
    data() {
      return {};
    },
    beforeRouteEnter(to, from, next) {
      // this is not defined so have to access store directely
      store.dispatch('showAuthorsStartingWith', to.params.letter)
        .then(() => next());
    },
    beforeRouteUpdate(to, from, next) {
      store.dispatch('showAuthorsStartingWith', to.params.letter)
        .then(() => next());
    },
    computed: {
      ...mapState([
        'page',
        'pagerLimit',
        'results',
        'totalResults',
        'fetching',
        'selectedDats',
      ]),
      ...mapGetters([
        'datWithKey',
      ]),
      subtitle() {
        if (this.selectedDats.length === 0) return 'From all libraries';
        if (this.selectedDats.length === 1) return `From ${this.datWithKey(this.selectedDats[0]).name}`;
        return `From ${this.selectedDats.length} selected libraries`;
      },
    },
    methods: {
      ...mapMutations(['setPage']),
      ...mapActions(['getAuthors']),
      goToPage(page) {
        if (!this.fetching) { // stop page from reloading before new search is updated - this is a bit hacky
          this.setPage(page);
          this.getAuthors(this.$route.params.letter);
        }
      },
      showTextsBy(author) {
        // @TODO: use url param to pass to container and do action from there.
        this.$router.push({ name: 'byAuthor', params: { author } });
      },
    },
};
</script>
