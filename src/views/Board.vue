<template>
  <v-container
    fluid
  >
    <h2>{{ name }}</h2>
    <v-row
      align="center"
      justify="center"
    >
      <board-lists-create-form />
    </v-row>
    <v-row
      align="center"
      justify="center"
    >
      <board-lists-list :lists="lists" />
    </v-row>

    <v-progress-circular
      v-if="loadingBoard || loadingLists"
      indeterminate
      color="primary"
    />
    {{ displaySidebar }}
    <v-btn @click="displaySidebar = !displaySidebar">
      toggle
    </v-btn>
    <board-sidebar :show="displaySidebar" />
  </v-container>
</template>
<script>
import { mapActions, mapGetters, mapState } from 'vuex'
import BoardListsCreateForm from '../components/Board/ListsCreateForm'
import BoardListsList from '../components/Board/ListsList'
import BoardSidebar from '../components/Board/Sidebar'

export default {
  name: "Board",
  components: {
    BoardListsCreateForm,
    BoardListsList,
    BoardSidebar
  },
  data: () => ({
    name: "",
    displaySidebar: false
  }),
  computed: {
    ...mapState("boards", { loadingBoard: "isGetPending" }),
    ...mapState("lists", { loadingLists: "isFindPending" }),
    ...mapGetters("lists", { findListsInStore: "find" }),
    lists () {
      const { data: lists } = this.findListsInStore({ query: { boardId: this.$route.params.id } })
      return lists
    }
  },
  async created () {
    try {
      const response = await this.getBoard(this.$route.params.id)
      this.getLists({query: { boardId: this.$route.params.id }})
      const cards = await this.getAllCardsFromThisBoard({query: { boardId: this.$route.params.id }})
      console.log(cards)
      this.name = response.name
    } catch (error) {
      console.error(error)
    }
  },
  methods: {
    ...mapActions("boards", { getBoard: "get" }),
    ...mapActions("lists", { getLists: "find" }),
    ...mapActions("cards", { getAllCardsFromThisBoard: "find" })
    
  }
}
</script>