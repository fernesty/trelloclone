<template>
  <div class="modal-wrapper" @click="close" >
    <div class="modal" >
      <div v-if="cardNameInp != card.id" @click="setInpVal(card.name, card.id)" class="title">
        <p class="text" >{{card.name}} </p>
        
        <div class="btns" >
          <button v-if="!card.isArchive" 
            @click="$emit('makeCardIsArchive', card.id)" 
            class="delete archive" >Перенести в архив</button>

          <button v-if="card.isArchive"  @click="$emit('removeCardFromArchive', card.id)" class="delete archive" >Убрать из архива</button>
          <button @click="$emit('deleteCard', card.id)" class="delete" >Удалить</button>
        </div>
      </div>
      <div v-else >
        <input 
          type="text" 
          v-model="editedcardName"
          class="inp_edit inp_w"  >

        <button class="inp_edit btn" @click="save()" >Сохранить</button>
      </div>
    </div>

  </div>
</template>

<script>
let cardId = null

export default {
  name: 'Modal',
  props: {
    card: Object
  },

  data() {
    return {
      cardNameInp: "",
      editedcardName: ""
    }
  },  

  methods: {
    setInpVal(name, id) {
      this.cardNameInp = id
      this.editedcardName = name
    },

    save() {
      this.$emit('renameCard', {id: this.cardNameInp, name: this.editedcardName})

      this.cardNameInp = ""
      this.editedcardName = ""
    },

    close(e) {
      if (e.target.className == 'modal-wrapper') this.$emit('closeModal')
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

.title {
  width: 100%;
  display: flex;
  justify-content: space-between;
}
.modal-wrapper {
  background-color: rgba(0, 0, 0, 0.4);
  width: 100vw;
  height: 100vh;

  position: absolute;
  top: 0;
  bottom: 0;

  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 999;
}

.modal {

  min-width: 600px;
  min-height: 400px;

  background: #fff;
  border-radius: 10px;
  padding: 20px;
}

.inp_edit {
  background-color: #dfe1e5;
  border-radius: 6px;
  border: none;
  padding: 6px;
  font-size: 18px;

  margin-right: 10px;
}

.btn {
  cursor: pointer;
}

.inp_w {
  width: 295px;
}


.delete {
  background-color: red;
  padding: 10px;
  border: none;
  border-radius: 6px;
  color: white;
  z-index: 999;
  cursor: pointer;
  margin-top: 10px;
}

.archive {
  background: #1677ff;
}

.btns {
  display: flex;
  flex-direction: column;
  align-items: flex-end;

  height: 100%;
}

.text {
  max-width: 460px;
  white-space: wrap;
  overflow: hidden; /* Обрезаем всё за пределами блока */
}
</style>
