<script>
import Modal from './Modal.vue';
export default {
  components: {
    Modal
  },
  data() {
    return {
      notes: [],
      showModal: false,
      adding: false,
      id: "",
      title: "",
      content: "",
      url:'http://localhost:8080'
    };
  },
  created() {
    this.GetNotes();
  },
  methods: {
    GetNotes() {
      fetch(`${this.url}/api/GetNotes`)
        .then(response => response.json())
        .then(data => {
          this.notes = data;
        })
        .catch(error => {
          console.error('Error:', error);
        });
    },
    DeleteNote(id) {
      fetch(`${this.url}/api/DeleteNote?id=${id}`, {
        method: 'DELETE'
      })
        .then(response => {
          if (!response.ok) {
            throw new Error('Error deleting note');
          }
          console.log('Note deleted successfully');
        })
        .catch(error => {
          console.error('Error:', error);
        });
      let index = this.notes.findIndex(item => item.id === id);
      if (index > -1) {
        this.notes.splice(index, 1);
      }
    },
    AddNoteSent(note) {
      fetch(`${this.url}/api/AddNote`, {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify(note)
      })
        .then(response => response.json())
        .then(result => {
          return (result);
        })
        .catch(error => {
          console.error(error);
        })
    },
    AddNote() {
      this.showModal = true;
      this.adding = true;
      this.id = "";
      this.title = "";
      this.content = "";
    },
    EditNote(note) {
      this.showModal = true;
      this.adding = false;
      this.id = note.id;
      this.title = note.title;
      this.content = note.content;
    },
    SendNote() {
      this.showModal = false;
      let note = { id: this.id, title: this.title, content: this.content };
      let id = this.AddNoteSent(note);
      note.id = this.id;
      if (this.adding) {
        this.notes.push(note);
      }
      else {
        let index = this.notes.findIndex(item => item.id === this.id);
        this.notes[index] = note;
      }
    }
  }
};
</script>
<template>
  <Modal :show="showModal" @close="SendNote()">
    <template #header>
      <h3>Add/Edit Note</h3>
      <h4 style="color:black">Title</h4>
      <input v-model="title">
      <h4 style="color:black">Content</h4>
      <input v-model="content">
    </template>
  </Modal>
  <div class="">
    <a class="h1 end-align" @click="AddNote()">Add Note</a>
  </div>
  <transition-group name="fade" tag="div" class="d-flex cards-group">
    <div v-for="note in notes" :key="note.id" class="fade-item d-flex">
      <div class="item d-flex">
        <div class="details">
          <h3>
            <div class="d-flex space-between">
              <a @click="EditNote(note)">Edit</a>
              <a @click="DeleteNote(note.id)">X</a>
            </div>
          </h3>
          <div class="">
            <h3>
              <div name="heading">{{ note.title }}</div>
            </h3>
            <br>
            <p>
              {{ note.content }}
            </p>
          </div>
        </div>
      </div>
    </div>
  </transition-group>
</template>
  
<style scoped>
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s;
}
.h1{
  font-size: 2rem;
}
.end-align{
  display: flex;
  flex-direction: row-reverse;
}
.fade-enter,
.fade-leave-to {
  opacity: 0;
}
.cards-group{
width: 90vw;
flex-wrap: wrap
}
.space-between{
  justify-content: space-between !important;
}
.d-flex{
  display: flex;
  justify-content: space-around;
}
.d-block{
  display: block;
}
.content {
      width: 100%; 
      height: 100%; 
      display: flex;
      align-items: center; 
      justify-content: center; 
    }
.item {
  margin-top: 2rem;
  min-width: 200px;
  height: 400px;
  overflow: auto;
  height: auto;
  display: flex;
  position: relative;
  border: 1px double #ff6550;
  border-radius: 10px;
  background-color: aliceblue;
  color: black;
}

.details {
  flex: 1;
  margin: 1rem;
}

h3 {
  font-size: 1.2rem;
  font-weight: 500;
  margin-bottom: 0.4rem;
  color: black;
}
</style>
  