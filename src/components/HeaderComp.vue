<template>
  <header>
    <h2>WeatherMeteo</h2>
    <p>Прогноз погоды по всему миру</p>
    <input type="text" placeholder="Введите населённый пункт" @keydown="isEnter($event)">
  </header>
</template>

<script>
export default {
  name: 'HeaderComp',
  data() {
    return {
      name: '',
    }
  },
  methods: {
    isEnter: function(event){
      if (event.keyCode == 13){
        this.name = event.target.value;
        this.$emit('search', this.name);
      }
    },
  }
}
</script>

<style lang="scss" scoped>

  header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    width: 100%;
    height: 70px;
    background-color: rgb(0, 0, 134);
    color: white;
    font-family: 'Open Sans', sans-serif;
    h2 {
      margin-left: 50px;
      width: 260px;
    }
    p {
      width: 290px;
      font-size: 18px;
      font-weight: bold;
    }
    input {
      margin-right: 50px;
      padding-left: 10px;
      width: 260px;
      height: 35px;
      border: 0px;
      border-radius: 5px;
      font-size: 16px;
      outline: none;
    }
  }

  @media(max-width: 480px) {
    header {
      justify-content: center;
      flex-wrap: wrap;
      height: 100px;
      h2 {
        margin-left: 0px;
        text-align: center;
      }
      p {
        display: none;
      }
      input {
        margin-right: 0px;
      }
    }
  }

</style>
