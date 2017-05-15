<template>
<div id="front" class="ui items segment padtop-6 container">

  <!-- <div class="ui left action fluid icon input">
    <input type="text" placeholder="Filter by title.." class="home-search-input" v-model="filterTitle">
  </div>
  <hr> -->
  <table class="ui compact celled definition table">
    <thead class="full-width">
      <tr>
        <th colspan="2">
          <button class="ui right floated small primary labeled icon button" v-on:click="showAddQuestion">
            <i class="user icon"></i> Add Questions
          </button>

        </th>
      </tr>
    </thead>
    <thead class="full-width">
      <tr>
        <th class="ui centered header">Interesting</th>
        <th class="ui centered header">Top Questions</th>

      </tr>
    </thead>
    <tbody>
      <tr v-for="post in listPost">
        <td class="collapsing">
          <div class="ui compact menu">
            <a class="item">
              <i class="icon star"></i> Votes
              <div class="floating ui red label">{{ post.votes.length }}</div>
            </a>
            <a class="item">
              <i class="icon heart"></i> Answers
              <div class="floating ui teal label">{{ post.answers.length }} </div>
            </a>
          </div>
        </td>
        <td>
          <h3 class="ui header"><a v-on:click="singlePost(post._id)"> {{ post.title }}</a></h3>
          <p> {{ post.description.substring(0, 100) }} <a v-on:click="singlePost(post._id)" class="ui horizontal label"> Read More</a></p>
					<br>
					<p>Asked by
            <a class="ui image label">
               <img src="../assets/small_avatar.jpg"> {{ post.asked_by.name }}
            </a></p>

        </td>
      </tr>
    </tbody>
		<br><br>
  </table>

	<div class="ui small modal four grid" id="modalAddQuestion">
    <i class="close icon"></i>
    <div class="ui center aligned header">
      <h3>Add Question</h3>
    </div>
    <div class="ui doubling stackable content grid container">
      <div class="content">
        <div class="ui fluid form">
          <div class="field">
						<label>Title</label>
            <input v-model="addQuestion.title" type="text" value="">
          </div>

          <div class="field">
						<label>Description</label>
						<textarea rows="5" v-model="addQuestion.description"></textarea>
          </div>
          <button class="ui button blue" v-on:click="insertQuestion">Submit</button>
        </div>
      </div>
    </div>
  </div>

</div>
</template>



<script>

export default {
  // name: 'posts',
  data() {
    return {
      listPost: [],
      filterTitle: '',
			addQuestion: {
				title : '',
				description: ''
			}
    }
  },
  methods: {
    listItems() {
      let self = this;
      axios.get('http://localhost:3000/questions', {
          headers: {
            token: localStorage.getItem('token')
          }
        })
        .then(response => {
          if (response.config.headers.token == null) {
            alert('Please login!')
          } else {
            self.listPost = response.data;
            console.log('postnya ', response.data);
          }
        })
        .catch(error => {
          alert('Please login!')
          console.log("Please login!")
        })
    },
    singlePost(id) {
      this.$router.push('/detail-post/' + id);
    },
		insertQuestion(){
			let self = this
      axios.post('http://localhost:3000/questions/', {
					title : self.addQuestion.title,
          description: self.addQuestion.description
        }, {
          headers: {
            token: localStorage.getItem('token')
          }
        })
        .then(response => {
          if (response.config.headers.token == null) {
            alert('Please login!')
          } else {
            self.listItems()
						$('#modalAddQuestion').modal('hide')
          }
        })
        .catch(error => {
          alert('Please login!')
          console.log("Please login!")
        })
		},
		showAddQuestion(){
			this.addQuestion.title = ''
			this.addQuestion.description = ''
			$('#modalAddQuestion').modal('show')
		}
  },
  created() {
    this.listItems()
  },
  computed: {
    filteredTitle: function() {
      let self = this;
      return self.listPost.filter(function(post) {
        return post.title.indexOf(self.filterTitle) !== -1;
      })
    }
  }

} // end of export
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
