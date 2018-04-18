<template>
  <div id="app" class="row">
    <div v-for="list in original_lists" class="col-3">
      <h6>{{ list.name }}</h6>
      <hr/>
      <div v-for="(card, index) in list.cards" class="card card-body mb-3">
        {{ card.name }}
      </div>

      <div class="card card-body">
        <!-- because of v-model messages[list.id] is synced to whatever inputted in the textarea -->
        <textarea class="form-control" v-model="messages[list.id]"></textarea>
        <!-- once clicked the called function will trigger a database update -->
        <button v-on:click="submitMessages(list.id)" class="btn btn-primary">Add</button>
      </div>
    </div>
  </div>
</template>

<script>
// the step that vue takes to talk to Rails database is through ajax call
// would have done the same in asset pipeline or erb
// it's the eventlistener or callback that clears the text area and updates the vue data property
// would have done it the same way in asset pipeline. So the advantage of Vue.js remains to be seen
export default {
  props: ["original_lists"],
  data: function() {
    return {
      messages: {},
      lists: this.original_lists
    }
  },

  methods: {
    submitMessages: function(list_id) {
      var data = new FormData
      data.append("card[list_id]", list_id)
      data.append("card[name]", this.messages[list_id])

      Rails.ajax({
        url: "/cards",
        type: "POST",
        data: data,
        dataType: "json",
        // beforeSend: function() { return true },
        success: (data) => {
          const index = this.lists.findIndex(item => item.id == list_id)
          // push the card to vue data so it is shown on the page
          this.lists[index].cards.push(data)
          // clear the textarea
          this.messages[list_id] = undefined
        }
      })
    }
  }
}
</script>

<style scoped>
p {
  font-size: 2em;
  text-align: center;
}
</style>
