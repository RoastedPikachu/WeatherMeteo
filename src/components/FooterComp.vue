<template>
  <footer>
    <p>Сайт создан <a href="">RoastedPikachu</a></p>
  </footer>
</template>

<script>
export default {
  name: 'FooterComp',
}
</script>

<style lang='scss' scoped>

  footer {
    display: flex;
    justify-content: center;
    align-items: center;
    margin-top: 20px;
    width: 100%;
    height: 50px;
    background-color: rgb(0, 0, 134);
    font-family: 'Mono Sans', sans-serif;
    p {
      font-size: 18px;
      color: white;
      a {
        color: white;
        text-decoration: none;
        cursor: pointer;
        transition: 400ms ease;
      }
      a:hover {
        color: rgb(206, 41, 41);
      }
    }
  }

  @media(max-width: 450px) {
    footer {
      margin-top: 50px;
      font-family: 'Mono Sans', sans-serif;
      p {
        font-size: 16px;
        color: white;
        a {
          color: white;
          text-decoration: none;
          cursor: pointer;
          transition: 400ms ease;
        }
        a:hover {
          color: rgb(206, 41, 41);
        }
      }
    }
  }

</style>
