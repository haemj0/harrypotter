<script>
export default {
  data() {
    return {
      hero: [],
      heroWithoutSort: [],
      selectedCharacter: null,
      selectSort: '',
      currentSearch: '',
      SortOption: [
        {value: 'without', name:'Без сортировки'},
        {value: 'name', name:'По имени'},
        {value: 'species', name:'По сущности'},
        {value: 'yearOfBirth', name:'По дате рождения'},
      ],

      selectSortt: '',
      SortOptionn: [
        {value: 'name1', name:'По возрастанию'},
        {value: 'name2', name:'По убыванию'}
      ],
      Filter: '',
      filterOption: [
        {value: 'all', name:'Все'},
        {value: 'gr', name:'Гриффиндор'},
        {value: 'sl', name:'Слизерин'},
        {value: 'kg', name:'Когтевран'},
        {value: 'puf', name:'Пуффендуй'}
      ],

      Filter2: '',
      filterOption2: [
        {value: 'all', name:'Все'},
        {value: 'alive', name:'Живой(-ая)'},
        {value: 'death', name:'Мертвый(-ая)'}
      ],
      favorites: [],
      wizard: 0,
      human: 0,
      registr: false,
      avtor: false,
      like: false,
      login: '',
      password: '',
      name: '',
      fname: '',
      patron: '',
      phone: '',
      age: '',
      gender: '',
      liked: [],
      users: [
        {
          FIO: 'Борисова Анна Владимировна',
          login: 'haemj',
          password: '123',
          phone: '8(999)888-88-99',
          age: '19',
          gender: 'Женский',
          liked: ['Слизерин', 'Пуффендуй']
        }
      ],
      avtFIO: '',
      avttFIO: false,
      forma: true,
      activeUser: {},
      userModal: false
    }
  },
  methods: {
    async Hero() {
      const res = await fetch('https://hp-api.onrender.com/api/characters')
      this.hero = await res.json()
      this.heroWithoutSort = this.hero
    },

    openModal(character) {
      this.selectedCharacter = character
    },
    closeModal() {
      this.selectedCharacter = null
    },

    myFavorite(character, event) {
      event.stopPropagation()

      const index = this.favorites.findIndex(item => item.name === character.name)

      if (index !== -1) {
        const removed = this.favorites.splice(index, 1)[0]
        if (removed.wizard === true) this.wizard--
        else this.human--
      }
      else {
        this.favorites.push(character)
        if (character.wizard === true) this.wizard++
        else this.human++
      }
      console.log(this.favorites)
    },
    modalReg() {
      this.registr = true
    },
    closeReg() {
      this.registr = false
      this.avtor = false
    },
    modalAvt() {
      this.avtor = true
    },

    Regist(event) {
      event.preventDefault()
      let flag = true
      for (let i=0; i<this.users.length; i++) {
        if (this.users[i].login == this.login) {
          alert('Пользователь с таким логином уже существует')
          this.login = ''
          flag = false
        }
      }
      const mask = /^8\(\d{3}\)\d{3}-\d{2}-\d{2}$/
      const mask2 = /^[А-ЯЁ][а-яё]{1,}$/
      const mask3 = /^[А-ЯЁ][а-яё]{2,}$/
      if (!mask3.test(this.fname)) {
        alert('Неверный формат фамилии. Необходимый формат: первая буква заглавная, все буквы русские, длина имени не меньше 2 символов')
        this.fname = ''
      }
      if (!mask2.test(this.name)) {
        alert('Неверный формат имени. Необходимый формат: первая буква заглавная, все буквы русские, длина имени не меньше 1 символа')
        this.name = ''
      }
      if (!mask3.test(this.patron)) {
        alert('Неверный формат отчества. Необходимый формат: первая буква заглавная, все буквы русские, длина имени не меньше 2 символов')
        this.patron = ''
      }
      if(!mask.test(this.phone)) {
        alert('Неверный формат номера телефона. Введите в формате 8(XXX)XXX-XX-XX')
        this.phone = ''
      }
      if(this.age<10) {
        alert('Возраст не менее 10 лет')
        this.age = ''
      }
      if (document.getElementById('w').checked) {
        this.gender = 'Женский'
      } else if (document.getElementById('m').checked) {
        this.gender = 'Мужской'
      }
      if (flag && mask.test(this.phone) && mask2.test(this.name) && mask3.test(this.fname) && mask3.test(this.patron) && this.age>=10) {
        const user = {
          FIO: this.fname + ' ' + this.name + ' ' + this.patron,
          login: this.login,
          password: this.password,
          phone: this.phone,
          age: this.age,
          gender: this.gender,
          liked: this.liked
        }
        this.users.push(user)
        this.registr = false
        alert('Пользователь зарегистрирован')
        this.login = ''
            this.password = ''
            this.name = ''
            this.fname = ''
            this.patron = ''
            this.phone = ''
            this.age = ''
            this.gender = ''
            this.liked = []
        document.getElementById('w').checked = false
        document.getElementById('m').checked = false
      }
      console.log(this.users)

    },

    Avtoriz(event) {
      event.preventDefault()
      console.log(this.login, this.password)
      let userAvt = 0
      if (this.users.length==0) {
        alert('В базе данных нет зарегистрированных пользователей')
      }
      else {
        for (let i=0; i<this.users.length; i++) {
          if (this.users[i].login==this.login && this.users[i].password==this.password) {
            userAvt=this.users[i];
            this.avtor = false
            document.querySelectorAll(".reg").innerHTML += this.users[i].FIO
            this.activeUser = this.users[i]
            this.avtFIO = this.users[i].FIO
            this.like = true
            this.avttFIO = true
            this.forma = false

            this.login = ''
            this.password = ''
            break
          }

          }
        console.log(this.activeUser)
        } if (userAvt == 0) {
          alert("Пользователь не найден");
        this.login = ''
        this.password = ''
      }


    },

    modalUser() {
      this.userModal = true
    },

    exit() {
      this.avttFIO = false
      this.like = false
      this.forma = true
      this.userModal = false
      this.activeUser = {}
      this.favorites = []
      this.human = 0
      this.wizard = 0
    }

  },

  computed: {
    sortedHero() {
      let array = []
      let sortHeros = []
      if (this.selectSort==='without') {
        sortHeros = this.heroWithoutSort
      } else {
      sortHeros = [...this.hero].sort((p1, p2) => {
        if (typeof(p1[this.selectSort])=='number') {
          array = p1[this.selectSort]-p2[this.selectSort];
        } else {
          array = p1[this.selectSort]?.localeCompare(p2[this.selectSort]);
        }
        if (this.selectSortt === 'name2') {
          return -array;
        } else {
          return array;
        }
      })
      }
      return sortHeros
    },

    searchHeroesName() {
      return this.sortedHero.filter(hero=>
          hero.name.toLowerCase().includes(this.currentSearch.toLowerCase()) ||
          hero.patronus.toLowerCase().includes(this.currentSearch.toLowerCase()) ||
          hero.actor.toLowerCase().includes(this.currentSearch.toLowerCase()));
    },

    filterHeroesID() {
      return this.searchHeroesName.filter(hero=>{
        if (this.Filter !== 'all') {
          if (this.Filter === 'gr') return hero.house === 'Gryffindor'
          if (this.Filter === 'sl') return hero.house === 'Slytherin'
          if (this.Filter === 'kg') return hero.house === 'Ravenclaw'
          if (this.Filter === 'puf') return hero.house === 'Hufflepuff'
        }
        return true
      })
          .filter(hero => {
            if (this.Filter2 === 'alive') return hero.alive == true
            if (this.Filter2 === 'death') return hero.alive == false
            return true
          })
    }

  },
  mounted() {
    this.Hero()
  }
}
</script>


<template>
  <div class="container">
    <div class="head">
      <div class="reg">
        <h4 v-if="avttFIO" class="h4reg" @click="modalUser">Пользователь: {{this.avtFIO}}</h4>
        <div v-if="userModal" class="modUs" @click.self="userModal = false">
          <div class="modal-card" @click.stop style="display: grid">
            <h2>Личный кабинет</h2>
            <p><strong>ФИО:</strong> {{ this.activeUser.FIO}}</p><br>
            <p><strong>Логин:</strong> {{ this.activeUser.login}}</p><br>
            <p><strong>Телефон:</strong> {{ this.activeUser.phone}}</p><br>
            <p><strong>Возраст:</strong> {{ this.activeUser.age}}</p><br>
            <p><strong>Пол:</strong> {{ this.activeUser.gender}}</p><br>
            <p><strong>Любимые факультеты:</strong> {{ this.activeUser.liked?.join(', ') || 'Не выбрано' }}</p>

            <button @click="exit">Выйти из аккаунта</button>
          </div>
        </div>
        <div v-if="forma" class="reg">
        <button @click="modalAvt">Авторизация</button>
        <div v-if="avtor"  @click="closeReg" class="modAvt">

          <form @submit.prevent="Avtoriz" @click.stop>
            <h1>Авторизация</h1>
            <input placeholder="Введите логин" required v-model="login">
            <input type="password" placeholder="Введите пароль" required v-model="password">
            <button type="submit">Авторизоваться</button>
          </form>
        </div>


        <button @click="modalReg">Регистрация</button>
        <div v-if="registr"  @click="closeReg" class="modReg">

          <form @submit.prevent="Regist" @click.stop>

          <h1>Регистрация</h1>
              <input placeholder="Введите логин" required v-model="login">
              <input type="password" placeholder="Введите пароль" required v-model="password">
              <input placeholder="Введите фамилию" required v-model="fname">
              <input placeholder="Введите имя" required v-model="name">
              <input placeholder="Введите отчество" required v-model="patron">
              <input placeholder="Введите номер телефона" required v-model="phone">
              <input type="number" placeholder="Укажите возраст" required v-model="age">
              <h4>Выберите пол</h4>
              <div class="flexReg">
              <div class="flexReg">
                <label for="m">Мужской</label>
                <input type="radio" name="gender" id="m" required>
              </div>
              <div class="flexReg">
                <label for="w">Женский</label>
                <input type="radio" name="gender" id="w" required>
              </div>
              </div>

              <h4>Выберите любимые факультеты</h4>
              <div class="flexReg">
                <div class="flexReg">
                  <label for="g">Гриффиндор</label>
                  <input type="checkbox" id="g" value="Гриффиндор" v-model="liked">
                </div>
                <div class="flexReg">
                  <label for="s">Слизерин</label>
                  <input type="checkbox" id="s" value="Слизерин" v-model="liked">
                </div>
                <div class="flexReg">
                  <label for="h">Пуффендуй</label>
                  <input type="checkbox" id="h" value="Пуффендуй" v-model="liked">
                </div>
                <div class="flexReg">
                  <label for="r">Когтевран</label>
                  <input type="checkbox" id="r" value="Когтевран" v-model="liked">
                </div>
              </div>
            <button type="submit">Зарегистрироваться</button>

          </form>

        </div>
        </div>
      </div>

      <div>
      <h1>Герои мира Гарри Поттера</h1>
      <p class="prim">*Поиск воспроизводится по имени актера, персонажа и патронуса</p>
      </div>

      <div class="follow" v-if="like">
        <h2>Избранное</h2>
        <p>Магов: {{this.wizard}}</p>
        <p>Не магов: {{this.human}}</p>
      </div>
    </div>
  <div class="sort">
    <div class="sortsort">
      <select  v-model="selectSort">
        <option disabled value="">Выберите параметр сортировки</option>
        <option v-for="option in SortOption"
                :key="option.value"
                :value="option.value">
          {{option.name}}
        </option>
      </select>

      <select  v-model="selectSortt">
        <option disabled value="">Выберите тип сортировки</option>
        <option v-for="option in SortOptionn"
                :key="option.value"
                :value="option.value">
          {{option.name}}
        </option>
      </select>
    </div>

      <input type="text" placeholder="Поиск" v-model="currentSearch">

      <select v-model="Filter">
        <option disabled value="">Выберите тип фильтрации</option>
        <option v-for="option in filterOption" :value="option.value">{{option.name}}</option>
      </select>

    <select v-model="Filter2">
      <option disabled value="">Выберите тип фильтрации</option>
      <option v-for="option in filterOption2" :value="option.value">{{option.name}}</option>
    </select>
  </div>

    <div class="card-grid">
      <div class="card" v-for="character in filterHeroesID" @click="openModal(character)">
        <img :src="character.image" class="image" />
        <button class="foll" @click="myFavorite(character, $event)" v-if="like">
          {{ favorites.some(item => item.name === character.name) ? 'Убрать из избранного' : 'Добавить в избранное' }}
        </button>
        <div class="card-content">
          <h2>{{ character.name }}</h2>
          <p><strong>Пол:</strong> {{character.gender || 'Не указано'}}</p>
          <p><strong>Факультет:</strong> {{character.house || 'Не указано' }}</p>
          <p><strong>Патронус:</strong> {{character.patronus || 'Не указано' }}</p>
          <p><strong>Актер:</strong> {{character.actor || 'Не указано'}}</p>
          <p><strong>Цвет глаз:</strong> {{character.eyeColour || 'Не указано'}}</p>
          <p><strong>Цвет волос:</strong> {{character.hairColour || 'Не указано'}}</p>
          <p v-if="character.alive === true"><strong>Статус жизни:</strong> Жив(a)</p>
          <p v-if="character.alive === false"><strong>Статус жизни:</strong> Мeртв(a)</p>
        </div>
      </div>
    </div>


    <div v-if="selectedCharacter" class="modal-overlay" @click.self="closeModal">
      <div class="modal-card">
        <img :src="selectedCharacter.image" class="modal-image" />
        <div class="modal-content">
          <h2>{{ selectedCharacter.name }}</h2>
          <p><strong>Альтернативные имена:</strong> {{ selectedCharacter.alternate_names.join(", ") || 'Не указано'}}</p>
          <p><strong>Сущность:</strong> {{ selectedCharacter.species || 'Не указано'}}</p>
          <p><strong>Пол:</strong> {{ selectedCharacter.gender || 'Не указано'}}</p>
          <p><strong>Факультет:</strong> {{ selectedCharacter.house || 'Не указано' }}</p>
          <p><strong>Дата рождения:</strong> {{ selectedCharacter.dateOfBirth || 'Не указано'}}</p>
          <p><strong>Год рождения:</strong> {{ selectedCharacter.yearOfBirth || 'Не указано' }}</p>
          <p v-if="selectedCharacter.wizard === true"><strong>Волшебник или нет:</strong> Да</p>
          <p v-if="selectedCharacter.wizard === false"><strong>Волшебник или нет:</strong> Нет</p>
          <p><strong>Род:</strong> {{ selectedCharacter.ancestry || 'Не указано'}}</p>
          <p><strong>Цвет глаз:</strong> {{ selectedCharacter.eyeColour || 'Не указано'}}</p>
          <p><strong>Цвет волос:</strong> {{ selectedCharacter.hairColour || 'Не указано'}}</p>
          <p><strong>Палочка:</strong> Дерево: {{ selectedCharacter.wand.wood || 'Не указано'}} <br>
            Сердцевина: {{ selectedCharacter.wand.core || 'Не указано'}} <br>
            Длина: {{ selectedCharacter.wand.length || 'Не указано'}}</p>
          <p v-if="selectedCharacter.hogwartsStudent===true"><strong>Студент Хогвартса:</strong> Да</p>
          <p v-if="selectedCharacter.hogwartsStudent===false"><strong>Студент Хогвартса:</strong> Нет</p>
          <p v-if="selectedCharacter.hogwartsStaff===true"><strong>Работник Хогвартса:</strong> Да</p>
          <p v-if="selectedCharacter.hogwartsStaff===false"><strong>Работник Хогвартса:</strong> Нет</p>
          <p><strong>Патронус:</strong> {{ selectedCharacter.patronus || 'Не указано'}}</p>
          <p><strong>Актер:</strong> {{ selectedCharacter.actor || 'Не указано'}}</p>
          <p><strong>Другие актеры:</strong> {{ selectedCharacter.alternate_actors.join(", ") || 'Не указано'}}</p>
          <p v-if="selectedCharacter.alive === true"><strong>Статус жизни:</strong> Жив(a)</p>
          <p v-if="selectedCharacter.alive === false"><strong>Статус жизни:</strong> Мeртв(a)</p>
        </div>
      </div>
    </div>
  </div>
</template>



<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Cinzel+Decorative:wght@700&display=swap');

* {
  font-family: 'Arial', sans-serif;
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

body {
  background-color: #121212;
  color: #e0e0e0;
}

.container {
  padding: 20px;
  min-height: 1000px;
  background: url('https://avatars.mds.yandex.net/i?id=f8743bb9cbb18b8529dacd60f3b0c6d8_l-5220723-images-thumbs&n=13') no-repeat center center;
  background-attachment: fixed;
  background-color: #000000;
  background-size: auto;
}

h1 {
  font-family: 'Cinzel Decorative', cursive;
  font-size: 40px;
  text-align: center;
  margin-top: 20px;
  margin-bottom: 10px;
  color: #ffd700;
}

.prim {
  text-align: center;
  color: #bbbbbb;
  margin-bottom: 20px;
}

.head {
  display: grid;
  align-items: center;
  position: relative;
}

.reg {
  position: absolute;
  top: 0;
  left: 20px;
  display: grid;
  gap: 10px;
}

button {
  border: 1px solid #424242;
  background: #232323;
  color: #e0e0e0;
  border-radius: 8px;
  padding: 8px 16px;
  cursor: pointer;
  transition: background 0.3s;
}

button:hover {
  background: #424242;
}

.foll {
  display: block;
  margin: 10px auto 0 auto;
  background-color: #424242;
  color: #ffd700;
  font-weight: bold;
  border: none;
  padding: 8px 12px;
  border-radius: 6px;
  transition: background-color 0.3s;
}

.foll:hover {
  background-color: #666666;
}

.follow {
  position: absolute;
  top: 0;
  right: 20px;
  text-align: right;
}

.follow h2 {
  color: #ffd700;
  margin-bottom: 5px;
}

.follow p {
  color: #ffffff;
  margin: 2px 0;
}

.card-grid {
  display: flex;
  flex-wrap: wrap;
  gap: 20px;
  justify-content: center;
}

.card {
  width: 260px;
  background: #2a2a2a;
  border-radius: 12px;
  box-shadow: 0 0 20px rgba(255, 215, 0, 0.1);
  transition: transform 0.3s ease, box-shadow 0.3s ease;
  color: #f0f0f0;
}

.card:hover {
  transform: translateY(-5px);
  box-shadow: 0 0 25px rgba(255, 215, 0, 0.3);
}

.image {
  width: 100%;
  height: 320px;
  border-radius: 12px 12px 0 0;
  object-fit: cover;
}

.card-content {
  padding: 15px;
  font-size: 14px;
}

.card-content p {
  margin-top: 5px;
  margin-left: 0;
}

h2 {
  font-family: 'Cinzel Decorative', cursive;
  font-size: 20px;
  margin-bottom: 10px;
  color: #ffd700;
}

.sort {
  display: flex;
  flex-wrap: wrap;
  gap: 12px;
  justify-content: center;
  margin-bottom: 30px;
}

.sort select,
.sort input {
  padding: 10px 14px;
  border-radius: 6px;
  border: 1px solid #555555;
  background-color: #333333;
  color: #eeeeee;
  font-size: 14px;
}

.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  bottom: 0;
  right: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(15, 15, 15, 0.85);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1;
}

.modal-card {
  display: flex;
  background: #2a2a2a;
  border-radius: 12px;
  padding: 20px;
  width: 1000px;
  max-height: 90vh;
  color: #eeeeee;
  box-shadow: 0 0 30px rgba(255, 215, 0, 0.2);
}

.modUs .modal-card {
  width: 400px;
  padding: 20px;
  background: #2a2a2a;
  color: #e0e0e0;
  border-radius: 12px;
  box-shadow: 0 0 20px rgba(255, 215, 0, 0.2);
  display: grid;
  gap: 10px;
}

.modUs .modal-card h2 {
  font-size: 22px;
  margin-bottom: 10px;
  color: #ffd700;
}

.modUs .modal-card p {
  font-size: 14px;
  margin: 5px 0;
}

.modUs .modal-card button:hover {
  background-color: #555;
}

.modal-image {
  height: 700px;
  object-fit: cover;
  border-radius: 8px;
  margin-right: 20px;
}

.modal-content {
  display: grid;
}

.modal-content h2 {
  margin-bottom: 10px;
  font-size: 24px;
  color: #ffd700;
}

.modReg,
.modAvt,
.modUs{
  left: 0;
  right: 0;
  top: 0;
  bottom: 0;
  background: rgba(0, 0, 0, 0.8);
  position: fixed;
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 2;
}

form {
  display: grid;
  background: #2a2a2a;
  border-radius: 12px;
  padding: 20px;
  width: 500px;
  color: #e0e0e0;
  box-shadow: 0 0 25px rgba(255, 215, 0, 0.2);
}

form input {
  padding: 10px;
  margin: 5px 0;
  border-radius: 6px;
  border: 1px solid #444;
  background-color: #1b1b1b;
  color: #fff;
}

.flexReg {
  display: flex;
  align-items: center;
  gap: 10px;
}

h4 {
  margin: 10px 0 5px;
  color: #ffd700;
}

.h4reg {
  text-align: center;
  cursor: pointer;
  color: #ffd700;
  font-weight: bold;
  width: 300px;
}



</style>
