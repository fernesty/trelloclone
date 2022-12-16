<template>

    <header class="header" >

      <img @click="isSidebar = !isSidebar" class="menu" src="./assets/menu.svg" alt="">
      <p @click="setActiveBoard('asdf')" class="logo" >fernesti</p>

    </header>

    <div v-if="isSidebar" class="sidebar">
      <img @click="isSidebar = !isSidebar" class="menu" src="./assets/close.svg" alt="">

      <p class="add" @click="addBoard" >Создать</p>

      <div v-for="board in boards" :key="board.id" class="boards" >
        <div v-if="board.name != 'main'" class="board" >
          <p v-if="isEdit !== board.id" @click="setActiveBoard(board.id)" :class="ativeBoard === board.id ? 'name active' : 'name'" class="" >{{board.name}}</p>
          <div v-else >
            <input class="saveeditedbtn" type="text" v-model="editedText" >
            <button class="saveedited" @click="saveEditBoard" >Сохранть</button>
          </div>

          <div class="btns" >
            <img v-if="board.name !== 'main'" @click="edit(board.id, board.name)" class="edit" src="./assets/edit.png" alt="" >
            <img v-if="board.name !== 'main'" @click="deleteBoard(board.id)" class="close" src="./assets/close.svg" alt="" >
          </div>
        </div>
      </div>

      <hr class="line" >

      <div class="arvhive" >
        <p  @click="isShowArchive = !isShowArchive" class="title" >
          Архив
          <img :style="{transform: isShowArchive ? 'rotate(-180deg)' : 'rotate(0deg)'}" class="arrow" src="./assets/arrow.png" alt="">
        </p>

        <div v-if="isShowArchive" class="card__archive"
            v-for="card in getArchivetCards()"
            @click="openModal(card.id)" >
          <p>{{card.name}}</p>
        </div>
      </div>

    </div>

    <Modal
        v-if="isModal"
        :card="getCardById(isModal)"
        @closeModal="closeModal"
        @renameCard="renameCard"
        @deleteCard="deleteCard"
        @makeCardIsArchive="makeCardIsArchive"
        @removeCardFromArchive="removeCardFromArchive" />

    <div class="columns" >


      <Column
        v-for="column in columns"
        :key="column.name"
        :name="column.name"
        :id="column.id"
        :board="column.boardId"
        :activeBoard="ativeBoard"
        :cards="getCardsByCategory(column.id)"
        @moveCard="moveCard"
        @addCard="addCard"
        @renameColumn="renameColumn"
        @openModal="openModal"
        @deleteCol="deleteCol" />

      <div @click="addColumn" class="create_column" >+</div>
    </div>
</template>

<script>
import Column from './components/Column.vue'
import Modal from './components/Modal.vue'

export default {
  name: 'App',
  components: {
    Column,
    Modal
  },
  data() {
    return {
      columns: [],
      cards: [],

      isModal: false,

      isSidebar: false,
      boards: [],
      ativeBoard: null,
      isEdit: null,
      editedText: "",
      isShowArchive: false
    }
  },

  methods: {

    getArchivetCards() {
      const localcards = this.cards
      const arr = []

      for (let i = 0; i < localcards.length; i++) {
        const element = localcards[i];

        if (element.isArchive) arr.push(element)
      }

      return arr
    },

    edit(id, name) {
      this.isEdit = id
      this.editedText = name
    },

    makeCardIsArchive(id) {
      const localcards = this.cards

      for (let i = 0; i < localcards.length; i++) {
        const element = localcards[i];

        if (element.id == id) localcards[i].isArchive = true
      }

      this.cards = localcards
      this.closeModal()
      this.makeDamp()
    },

    removeCardFromArchive(id) {
      const localcards = this.cards

      for (let i = 0; i < localcards.length; i++) {
        const element = localcards[i];

        if (element.id == id) localcards[i].isArchive = false
      }

      this.cards = localcards
      this.makeDamp()
    },

    saveEditBoard() {
      const localBoards = this.boards

      for (let i = 0; i < localBoards.length; i++) {
        const element = localBoards[i];

        if (element.id == this.isEdit) localBoards[i].name = this.editedText
      }

      this.boards = localBoards
      this.isEdit = null
      this.editedText = ""

    },

    setActiveBoard(id) {
      const localBoards = this.boards;

      for (let i = 0; i < localBoards.length; i++) {
        const element = localBoards[i];

        if (element.id == id) this.ativeBoard = element.id
      }
      this.isSidebar = false
    },

    addBoard() {
      this.boards.push({"name": "Новая доска", id: this.getRandomID(0, 1679615) })
      this.makeDamp()
    },

    deleteBoard(id) {
      const localBoards = this.boards
      const newArray = []

      for (let i = 0; i < localBoards.length; i++) {
        const element = localBoards[i];

        if (element.id != id) newArray.push(element);
      }

      this.boards = newArray
      this.makeDamp()
    },

    deleteCol(id) {
      const localColumns = this.columns
      const newArr = []

      for (let i = 0; i < localColumns.length; i++) {
        const element = localColumns[i];


        if (element.id != id) newArr.push(element)
      }

      this.columns = newArr
      this.makeDamp()
    },

    deleteCard(id) {
      this.closeModal()
      const localcards = this.cards
      const newArr = []

      for (let i = 0; i < localcards.length; i++) {
        const element = localcards[i];


        if (element.id != id) newArr.push(element)
      }

      this.cards = newArr
      this.makeDamp()
    },

    getCardById(id) {
      const cards = this.cards

      for (let i = 0; i < cards.length; i++) {
        const element = cards[i];

        console.log(element);
        if (element.id == id) return element
      }

      return null
    },

    openModal(id) {
      this.isModal = id
    },

    closeModal() {
      this.isModal = false
    },

    getRandomID(min, max) {
      var int = Math.floor(Math.random() * (max - min + 1)) + min;
      return int.toString(36);
    },

    makeDamp() {
      localStorage.setItem("tr_cards", JSON.stringify(this.cards))
      localStorage.setItem("tr_boards", JSON.stringify(this.boards))
      localStorage.setItem("tr_columns", JSON.stringify(this.columns))
    },

    readDump() {
      this.cards = JSON.parse(localStorage.tr_cards || '[{"name":"куда пропали данные","id": "ls28","columnId": "yqeh"}]')
      this.columns = JSON.parse(localStorage.tr_columns || '[{"name":"Inbox","id": "yqeh", "boardId": "asdf"}]')
      this.boards = JSON.parse(localStorage.tr_boards || '[{"name":"main","id": "asdf"}]')

      console.log(this.cards);
    },

    renameCard({name, id}) {
      const cards = this.cards

      for (let i = 0; i < cards.length; i++) {
        const card = cards[i];

        if (card.id == id) cards[i].name = name
      }

      this.cards = cards
      this.makeDamp()
    },

    renameColumn({name, id}) {
      console.log(name, id);

      const columns = this.columns

      for (let i = 0; i < columns.length; i++) {
        const column = columns[i];

        if (column.id == id) columns[i].name = name
      }

      this.columns = columns;
      this.makeDamp()
    },

    addColumn() {
      console.log(this.ativeBoard);

      this.columns.push({
        name: "Новая колонка",
        id: this.getRandomID(0, 1679615),
        boardId: this.ativeBoard
      })

      this.makeDamp()
    },

    addCard(column) {
      this.cards.push({
        name: "Новая карточка",
        id: this.getRandomID(0, 1679615),
        columnId: column
      })
      this.makeDamp()
    },

    moveCard({column, cardId}) {
      const cards = this.cards

      for (let i = 0; i < cards.length; i++) {
        const element = cards[i];

        if (element.id === cardId) cards[i].columnId = column
      }

      this.cards = cards
      this.makeDamp()
    },

    getCardsByCategory(id) {
      const cards = this.cards
      const localCards = []

      for (let i = 0; i < cards.length; i++) {
        const element = cards[i];

        if (element.columnId === id) localCards.push(element)
      }

      return localCards;
    }
  },
  mounted() {
    this.readDump()
    this.ativeBoard = this.boards[0].id
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
}

body {
  margin: 0;
  padding: 0;
  background: rgb(241,130,163);
  background: linear-gradient(90deg, rgba(241,130,163,1) 0%, rgba(255,227,168,1) 100%);
  padding-top: 10px;
}

.columns {
  display: flex;
  align-items: baseline;
  overflow-y: scroll;

  padding-top: 60px;
  padding-left: 10px;
}

.create_column {
  background: #ebecf1;
  margin-top: 0px;
  height: 40px;
  width: 40px;
  border-radius: 6px;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  font-weight: bold;
  font-size: 20px;
}

.menu {
  margin-left: 5px;
  cursor: pointer;
}

.sidebar {
  background-color: #f5f7f9;
  width: 320px;
  height: 100vh;
  position: absolute;
  left: 0;
  top: 0;
  -webkit-box-shadow: 7px 4px 8px 0px rgba(34, 60, 80, 0.2);
  -moz-box-shadow: 7px 4px 8px 0px rgba(34, 60, 80, 0.2);
  box-shadow: 7px 4px 8px 0px rgba(34, 60, 80, 0.2);

  z-index: 9999;
}

.boards {
  padding-left: 20px;
}

.board {
  display: flex;
  justify-content: space-between;
  width: 93%;
}

.board .close {
  width: 10px;
  cursor: pointer;
}
.board p {
  margin: 0;
  padding: 0;
  margin-top: 8px;

  white-space: nowrap; /* Текст не переносится */
  overflow: hidden; /* Обрезаем всё за пределами блока */
  text-overflow: ellipsis; /* Добавляем многоточие */

  max-width: 80%;
}

.sidebar .menu {
  margin: 10px;
}

.active {
  text-decoration: underline;
}

.add {
  width: 90%;
  margin-left: 5%;
  height: 36px;
  border-radius: 6px;
  box-shadow: 0 8px 14px rgb(34 34 34 / 5%);

  display: flex;
  justify-content: center;
  align-items: center;
  background: #fff;
  cursor: pointer;
}

.edit {
  width: 14px;
  height: 14px;
  margin-right: 10px;
  cursor: pointer;
}

.btns {
  display: flex;
  align-items: center;
}

.header {
  background: white;
  width: 100%;
  height: 60px;
  position: absolute;
  top: 0;
  left: 0;

  display: flex;
  justify-content: space-between;
  align-items: center;
  box-sizing: border-box;

  padding-left: 20px;
  padding-right: 20px;
}

.logo {
  font-weight: bold;
  font-size: 24px;
}

.line {
  background: gray;
  width: 90%;
  margin-top: 20px;
  margin-bottom: 20px;
}

.arvhive{
  margin-left: 5%;
}
.arvhive .arrow {
  width: 18px;
  margin-left: 20px;
  transition: transform 0.3s;

}

.arvhive .title {
  display: flex;
  align-items: center;
  cursor: pointer;
}

.card__archive {
  background: #fff;
  border-radius: 6px;
  margin-top: 5px;
  width: 95%;
  min-height: 24px;

  display: flex;
  align-items: center;

  padding-left: 6px;
  padding-right: 6px;

  box-sizing: border-box;
  cursor: pointer;
  box-shadow: 0 8px 14px rgb(34 34 34 / 5%);
}

.card__archive p {
  font-size: 14px;
  margin: 0;
  padding: 2px;
  white-space: nowrap; /* Текст не переносится */
  overflow: hidden; /* Обрезаем всё за пределами блока */
  text-overflow: ellipsis; /* Добавляем многоточие */

}

.saveedited {
  height: 24px;
  margin-left: -1px;
  background: white;
  box-shadow: 0 8px 14px rgb(34 34 34 / 5%);
  border-radius: 2px;
  margin-left: 2px;
  border: none;

  box-sizing: border-box;
  margin-top: 5px;
  font-size: 14px;
}

.saveeditedbtn {
  height: 24px;
  margin-left: -1px;
  background: white;
  box-shadow: 0 8px 14px rgb(34 34 34 / 5%);
  border-radius: 2px;
  margin-left: 2px;
  border: none;

  box-sizing: border-box;
  margin-top: 5px;
  font-size: 14px;
  padding-left: 4px;
  margin-left: -2px;
}
</style>
