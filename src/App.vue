<script>

import axios from 'axios'

export default {
  data (){
    return{
      city: "",
      error: "",
      info: null,
      isLoading: false,
      token: "a86b011a4719480360159e0d15d14717"
    }
  },

  computed: {
    cityName(){
      return "\'" + this.city + "\'"
    },

    showTemp(){
      return "Температура: " + this.info.main.temp + "℃"
    },

    showFeelsLike(){
      return "Ощущается как: " + this.info.main.feels_like + "℃"
    },

    showMinTemp(){
      return "Минимальная температура: " + this.info.main.temp_min + "℃"
    },

    showMaxTemp(){
      return "Максимальная температура: " + this.info.main.temp_max + "℃"
    }
  },

  methods: {
    async getWeather() {  
    if (this.city.trim().length < 2) {
      this.error = "Нужно название более одного символа"
      return false
    }

    this.error = ""
    this.isLoading = true  
    this.info = null 

    try {
      const response = await axios.get(
        `https://api.openweathermap.org/data/2.5/weather?q=${this.city}&units=metric&appid=${this.token}`
      )
      this.info = response.data
    } catch (error) {
      if (error.response && error.response.status === 404) {
        this.error = "Город не найден, попробуйте другой"
      } else if (error.response) {
        this.error = "Ошибка сервера: " + error.response.status
      } else if (error.request) {
        this.error = "Не удалось получить ответ от сервера"
      } else {
        this.error = "Произошла ошибка при запросе"
      }
    } finally {
      this.isLoading = false  
    }
  }
  }
}
</script>

<template>

  <div class="author-holder">
    <h2 class="author-title">
      By Max Popov
    </h2>
  </div>

  <div class="wrapper__outer">
<div class="wrapper"> 
  <h1>Погодное приложение </h1>
  <p>Узнать погоду в {{ city == "" ? "вашем городе" : cityName}}</p>

  <input type="text" v-model="city"  placeholder="Введите ваш город">
<button 
  v-if="city != ''" 
  @click="getWeather()"
  :disabled="isLoading"
>
  {{ isLoading ? 'Загрузка...' : 'Получить погоду!' }}
</button>
   <button v-else disabled>Введите название города</button>

   <p class="error">
    {{ error }}
   </p>
   
  <div v-if="isLoading" class="loading">
    Загружаем данные...
  </div>

   <div v-if="info != null">
     <p>
      {{ showTemp }} 
    </p>

    <p>
      {{ showFeelsLike }}
    </p>

     <p>
      {{ showMinTemp }}
    </p>

     <p>
      {{ showMaxTemp }}
    </p>
   </div>

  
  
 </div>
  </div>
 
 

</template>


<style scoped>

body{
}

.error{
  color:  #d04040;
}

.wrapper__outer{
  width: 100%;

  padding-left: 12px;
  padding-right: 12px;
}

  .wrapper{
    display: flex;
    flex-direction: column;
    align-items: center;

    width: 900px;
    min-height: 500px;
    border-radius: 50px;
    padding: 20px;
    border: 1px solid #efe;
    background: #eff;

    text-align: center;

  }

  
  .wrapper h1{
    margin-top: 50px;
  }

  .wrapper p{
    margin-top: 20px;
  }

  .wrapper input{
    margin-top: 30px;
    background: transparent;

    border: 0;
    border-bottom: 2px solid #333;
    padding: 5px;
    font-size: 18px;
    outline: none;
  }

    .wrapper input:focus{
      border-bottom-color: #09ff09;
    }

    .wrapper button{
       padding: 5px;
    font-size: 18px;

    background: #09ff09;
    border-radius: 5px;
    cursor: pointer;

    margin-top: 10px;

    transition: .3s linear;
    }

    .wrapper button:hover{
      background: #9c9;
    }

    button:disabled{
      background: #efe;
    }

    .author-holder{
      width: 100%;
      display: flex;
      justify-content: center;
      
      position: absolute;

      padding: 20px;

      top: 0;
      left: 0;
    }

    .loading {
  margin: 20px 0;
  color: #000000;
}

@media screen and (max-width: 1024px) {
  
  .wrapper{
    width: 100%;
  }

  body{
    padding-left: 30px;
    padding-right: 30px;
  }

    .wrapper h1{
    margin-top: 0px;
  }

}

</style>
