<template>

  <div 
  class="column" 
  @drop="onDrop($event)"
  :id="id"
  v-if="board == activeBoard"
  @dragover.prevent
  @dragenter.prevent
  @mouseover="showDeleteBtn = true"
  @mouseleave="showDeleteBtn = false" >

    <p v-if="columnNameInp != id" :id="id" class="title" @click="setInpVal(name, id)" >{{name}}</p>
    
    <div v-else class="form" >
      <input 
        type="text"
        v-model="editedColumnName"
        class="inp_edit inp_w"  >

      <button class="inp_edit btn" @click="save()" >Сохранить</button>
    </div>

    <div v-for="card in cards" :key="card.name" >
      <div 
        :id="id"
        v-if="!card.isArchive"
        class="card"
        draggable="true"
        @dragstart="startDrag($event, card)" 
        @click="$emit('openModal', card.id)" >
        <p :id="id" >{{ card.name }}</p>
      </div>

    </div>

    <div @click="$emit('addCard', id)" :id="id" class="container_plus" >+</div>

    <button @click="$emit('deleteCol', id)" v-if="showDeleteBtn" class="delete" >Удалить</button>
  </div>

</template>

<script>
let cardId = null

export default {
  name: 'Column',
  props: {
    name: String,
    cards: Array,
    id: String,
    board: String,
    activeBoard: String
  },

  data() {
    return {
      columnNameInp: "",
      editedColumnName: "",

      showDeleteBtn: false
    }
  },  

  methods: {
    setInpVal(name, id) {
      this.columnNameInp = id
      this.editedColumnName = name
    },  

    save() {

      this.$emit('renameColumn', {id: this.columnNameInp, name: this.editedColumnName})

      this.columnNameInp = ""
      this.editedColumnName = ""
    },

    startDrag(evt, card) {
      this.dragbleElementId = card.id
      cardId = card.id
    },

    onDrop(evt) {
      this.$emit("moveCard", {column: evt.toElement.id, cardId})

      cardId = null
    },
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.column {
  background: #ebecf1;
  padding: 8px;
  margin: 5px;
  width: 260px;
  border-radius: 1px;
  position: relative;
}

.title {
  margin-bottom: 20px;
  margin-top: 0;
  font-weight: bold;
}

.card {
  background: #ffffff;
  border-radius: 5px;
  
  padding-left: 6px;
  padding-right: 6px;
  padding-top: 6px;
  padding-bottom: 6px;
  margin-top: 5px;
  cursor: grab;
  -webkit-box-shadow: 0px 2px 8px 0px rgba(34, 60, 80, 0.2);
  -moz-box-shadow: 0px 2px 8px 0px rgba(34, 60, 80, 0.2);
  box-shadow: 0px 2px 8px 0px rgba(34, 60, 80, 0.2);

  word-wrap: break-word;
}
.card p {
  margin: 0;
  padding: 0;
}
.container_plus {
  background: #ffffff;
  width: 100%;
  height: 40px;
  margin-top: 5px;
  border-radius: 6px;
  display: flex;
  align-items: center;
  justify-content: center;
  font-weight: bold;
  font-size: 24px;
  cursor: pointer;
}

.inp_edit {
  background-color: #fff;
  border-radius: 6px;
  border: none;
  padding: 6px;
  font-size: 18px;

  margin-right: 10px;
}

.btn {
  cursor: pointer;
  margin-top: 10px;
}

.inp_w {
  width: 175px;
}

.form {
  margin-bottom: 40px;
}

.delete {
  position: absolute;
  background-color: red;
  top: 8px;
  right: 5px;
  padding: 8.5px;
  border: none;
  border-radius: 6px;
  color: white;
  z-index: 999;
  cursor: pointer;
}
</style>
