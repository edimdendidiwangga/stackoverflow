<template>
<div id="front" class="ui items segment padtop-6 container">
  <div class="ui internally celled grid">
    <div class="row">
      <div class="two wide column">
        <div class="ui tiny statistics">
          <div class="orange statistic">
            <div class="value">
              {{ calcVote(post[0].votes) }}
            </div>
            <div class="label">
              Votes
            </div>

            <div class="extra" >
              <br>
              <button class="circular ui left green icon button"  v-on:click="upVoteQuestion(post[0]._id)">
								<i class="thumbs outline up icon"></i>
							</button>
              <button class="circular ui right red icon button" v-on:click="downVoteQuestion(post[0]._id)">
								<i class="thumbs outline down icon"></i>
			        </button>
            </div>
          </div>
        </div>
      </div>
      <div class="fourteen wide column">
        <h3>{{ post[0].title }}</h3>
        <p>{{ post[0].description }}</p>
        <p>Asked by :
          <a class="ui image label">
            <img src="../assets/small_avatar.jpg"> {{ post[0].asked_by.name }}
          </a>
          <button class="circular ui mini left blue icon button" v-if="usernameToken == post[0].asked_by.name" v-on:click="editQuestion(post[0]._id)">
						<i class="write icon"></i>
					</button>
          <button class="circular ui mini right red icon button" v-if="usernameToken == post[0].asked_by.name" v-on:click="deleteQuestion(post[0]._id)">
						<i class="trash outline icon"></i>
					</button>
        </p>
      </div>
    </div>
    <div class="row">
      <div class="sixteen wide column">
        <div class="ui small horizontal statistics">
          <div class="statistic">
            <div class="value">
              {{ post[0].answers.length }}
            </div>
            <div class="label">
              answers
            </div>
          </div>
        </div>

      </div>
    </div>
    <div class="row" v-for="answer in post[0].answers">
      <div class="two wide column">
        <div class="ui tiny statistics">
          <div class="orange statistic">
            <div class="value">
              {{ calcVote(answer.votes) }}
            </div>
            <div class="label">
              Votes
            </div>
            <div class="extra">
              <br>
              <button class="circular ui left green icon button" v-on:click="upVoteAnswer(answer._id)">
								<i class="thumbs outline up icon"></i>
							</button>
              <button class="circular ui right red icon button" v-on:click="downVoteAnswer(answer._id)">
								<i class="thumbs outline down icon"></i>
			        </button>
            </div>
          </div>
        </div>
      </div>
      <div class="fourteen wide column">
        <p>{{ answer.description }}</p>
        <p>Answer by :
          <a class="ui image label">
            <img src="../assets/small_avatar.jpg"> {{ answer.answered_by.name }}
          </a>
          <!-- <button class="circular ui mini left blue icon button" v-on:click="editAnswer(answer.id)">
						<i class="write icon"></i>
					</button> -->
          <button class="circular ui mini right red icon button" v-if="usernameToken == post[0].asked_by.name" v-on:click="deleteAnswer(answer._id)">
						<i class="trash outline icon"></i>
					</button>
        </p>
      </div>
    </div>

    <div class="row">
      <div class="sixteen wide column">
        <h3>Your Answer </h3>
        <div class="ui orange segment">
          <div class="ui form">

            <div class="field">
              <label>Input Text</label>
              <textarea rows="5" v-model="inputAnswer"></textarea>
              <br>
              <button class="ui blue button" v-on:click="replyQuestion(post[0]._id)">Submit</button>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>

  <div class="ui small modal four grid" id="modalEditQuestion">
    <i class="close icon"></i>
    <div class="ui center aligned header">
      <h3>Edit Question</h3>
    </div>
    <div class="ui doubling stackable content grid container">
      <div class="content">
        <div class="ui fluid form">
          <div class="field">
            <label>Title</label>
            <input v-model="editDataQuestion.title" type="text" value="">
          </div>
          <div class="field">
            <label>Description</label>
            <textarea rows="5" v-model="editDataQuestion.description"></textarea>
          </div>
          <button id="btnSubmitUpdateQuestion" class="ui button blue" v-on:click="updateQuestion">Update</button>
        </div>
      </div>
    </div>
  </div>

  <div class="ui small modal four grid" id="modalEditAnswer">
    <i class="close icon"></i>
    <div class="ui center aligned header">
      <h3>Edit Answer</h3>
    </div>
    <div class="ui doubling stackable content grid container">
      <div class="content">
        <div class="ui fluid form">
          <div class="field">
            <label>Description</label>
            <textarea rows="5" v-model="editDataAnswer.description"></textarea>
          </div>
          <button id="btnSubmitUpdateAnswer" class="ui button blue" v-on:click="updateAnswer(editDataAnswer.id)">Update</button>
        </div>
      </div>
    </div>
  </div>

  <div class="ui basic modal" id="deleteModalQuestion">
    <div class="ui icon header">
    <i class="trash outline icon"></i> Remove Questions
    </div>
    <div class="ui centered header content">
      <p>Are you sure delete this question?</p>
      <input type="hidden" v-model="deleteDataQuestion.id">
    </div>
    <div class="actions">
      <div class="ui red basic cancel inverted button">
        <i class="remove icon"></i> No
      </div>
      <div class="ui green ok inverted button" v-on:click="deleteQuestionTrue">
        <i class="checkmark icon"></i> Yes
      </div>
    </div>
  </div>

	<div class="ui basic modal" id="deleteModalAnswer">
    <div class="ui icon header">
      <i class="trash outline icon"></i> Remove Answer
    </div>
    <div class="ui centered header content">
      <p>Are you sure delete this question?</p>
      <input type="hidden" v-model="deleteDataAnswer.id">
    </div>
    <div class="actions">
      <div class="ui red basic cancel inverted button">
        <i class="remove icon"></i> No
      </div>
      <div class="ui green ok inverted button" v-on:click="deleteAnswerTrue">
        <i class="checkmark icon"></i> Yes
      </div>
    </div>
  </div>

</div>
</template>
<script>


export default {
  name: 'detailPost',
  props: ['id'],
  data() {
    return {
			usernameToken: localStorage.getItem('username'),
      post: {},
      inputAnswer: '',
      editDataQuestion: {
        title: '',
        description: ''
      },
      editDataAnswer: {
        id: '',
        description: ''
      },
      deleteDataQuestion: {
        id: ''
      },
			deleteDataAnswer: {
        id: ''
      }
    }
  },
  methods: {
    getPostId() {
      let self = this
      axios.get(`http://localhost:3000/questions/${this.id}`, {
          headers: {
            token: localStorage.getItem('token')
          }
        })
        .then(response => {
          if (response.config.headers.token == null) {
            alert('Please login!')
          } else {
            self.post = response.data
          }
        })
        .catch(error => {
          alert('Please login!')
          console.log("Please login!")
        })
    }, // end of getPostId
    upVoteQuestion(id) {
      let self = this
      axios.post(`http://localhost:3000/votes/question/${id}`, {
          vote: 1
        }, {
          headers: {
            token: localStorage.getItem('token')
          }
        })
        .then(response => {
          if (response.config.headers.token == null) {
            alert('Please login!')
          } else {
            if (response.data.validated) {
              self.getPostId()
            } else {
              alert('Anda sudah ngeVote !')
            }
            console.log('votenya ', response)
          }
        })
        .catch(error => {
          alert('Please login!')
          console.log("Please login!")
        })
    },
    downVoteQuestion(id) {
      let self = this
      axios.post(`http://localhost:3000/votes/question/${id}`, {
          vote: -1
        }, {
          headers: {
            token: localStorage.getItem('token')
          }
        })
        .then(response => {
          if (response.config.headers.token == null) {
            alert('Please login!')
          } else {
            if (response.data.validated) {
              self.getPostId()
            } else {
              alert('anda sudah ngeVote !')
            }
            console.log('votenya ', response)
          }
        })
        .catch(error => {
          alert('Please login!')
          console.log("Please login!")
        })
    },
    upVoteAnswer(id) {
      let self = this
      axios.post(`http://localhost:3000/votes/answer/${id}`, {
          vote: 1
        }, {
          headers: {
            token: localStorage.getItem('token')
          }
        })
        .then(response => {
          if (response.config.headers.token == null) {
            alert('Please login!')
          } else {
            if (response.data.validated) {
              self.getPostId()
            } else {
              alert('anda sudah ngeVote !')
            }
          }
        })
        .catch(error => {
          alert('Please login!')
          console.log("Please login!")
        })
    },
    downVoteAnswer(id) {
      let self = this
      axios.post(`http://localhost:3000/votes/answer/${id}`, {
          vote: -1
        }, {
          headers: {
            token: localStorage.getItem('token')
          }
        })
        .then(response => {
          if (response.config.headers.token == null) {
            alert('Please login!')
          } else {
            if (response.data.validated) {
              self.getPostId(id)
            } else {
              alert('anda sudah ngeVote !')
            }
            console.log('votenya ', response)
          }
        })
        .catch(error => {
          alert('Please login!')
          console.log("Please login!")
        })
    },
    calcVote(arr) {
      return arr.reduce((a, b) => {
        return a + b.vote
      }, 0)
    },
    replyQuestion(id) {
      let self = this
      axios.post(`http://localhost:3000/answers/${id}`, {
          description: self.inputAnswer
        }, {
          headers: {
            token: localStorage.getItem('token')
          }
        })
        .then(response => {
          if (response.config.headers.token == null) {
            alert('Please login!')
          } else {
            self.getPostId(id)
          }
        })
        .catch(error => {
          alert('Please login!')
          console.log("Please login!")
        })
    },
    editQuestion() {
      let self = this
      axios.get(`http://localhost:3000/questions/${this.id}`, {
          headers: {
            token: localStorage.getItem('token')
          }
        })
        .then(response => {
          if (response.config.headers.token == null) {
            alert('Please login!')
          } else {
            self.editDataQuestion.title = response.data[0].title
            self.editDataQuestion.description = response.data[0].description
            $('#modalEditQuestion').modal('show')
          }
        })
        .catch(error => {
          alert('Please login!')
          console.log("Please login!")
        })
    },
    updateQuestion() {
      let self = this
      axios.put(`http://localhost:3000/questions/${this.id}`, {
          title: self.editDataQuestion.title,
          description: self.editDataQuestion.description
        }, {
          headers: {
            token: localStorage.getItem('token')
          }
        })
        .then(response => {
          if (response.config.headers.token == null) {
            alert('Please login!')
          } else {
            self.getPostId()
            $('#modalEditQuestion').modal('hide')
          }
        })
        .catch(error => {
          alert('Please login!')
          console.log("Please login!")
        })
    },
    deleteQuestion(id) {
      this.deleteDataQuestion.id = id
      $('#deleteModalQuestion').modal('show')
    },
    deleteQuestionTrue() {
      let self = this
      axios.delete(`http://localhost:3000/questions/${this.id}`, {
          headers: {
            token: localStorage.getItem('token')
          }
        })
        .then(response => {
          if (response.config.headers.token == null) {
            alert('Please login!')
          } else {
            self.getPostId()
            this.$router.push('/posts');
          }
        })
        .catch(error => {
          alert('Please login!')
          console.log("Please login!")
        })
    },
    editAnswer(id) {
      let self = this
      axios.get(`http://localhost:3000/answers/${id}`, {
          headers: {
            token: localStorage.getItem('token')
          }
        })
        .then(response => {
          if (response.config.headers.token == null) {
            alert('Please login!')
          } else {
            self.editDataAnswer.description = response.data[0].description
            $('#modalEditAnswer').modal('show')
          }
        })
        .catch(error => {
          alert('Please login!')
          console.log("Please login!")
        })
    },
    updateAnswer(id) {
      let self = this
      axios.put(`http://localhost:3000/answers/${id}`, {
          description: self.editDataAnswer.description
        }, {
          headers: {
            token: localStorage.getItem('token')
          }
        })
        .then(response => {
          if (response.config.headers.token == null) {
            alert('Please login!')
          } else {
            self.getPostId()
          }
        })
        .catch(error => {
          alert('Please login!')
          console.log("Please login!")
        })
    },
    deleteAnswer(id) {
			this.deleteDataAnswer.id = id
      $('#deleteModalAnswer').modal('show')
    },
		deleteAnswerTrue(){
			let self = this
			let id_answer = this.deleteDataAnswer.id
      axios.delete(`http://localhost:3000/answers/${id_answer}`, {
          headers: {
            token: localStorage.getItem('token')
          }
        })
        .then(response => {
          if (response.config.headers.token == null) {
            alert('Please login!')
          } else {
            self.getPostId()
						$('#deleteModalAnswer').modal('hide')
          }
        })
        .catch(error => {
          alert('Please login!')
          console.log("Please login!")
        })
		}


  },
  created() {
    this.getPostId()
  }

}
</script>
<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
