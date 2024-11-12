<template>
  <div class="fundo">
  <div class="home">
    <div class="imagens">
      <div class="dom"></div>
      <div class="mais"></div>
      <div class="logo"></div>
    </div>
    <div class="bloco">
      <h1>BEM-VINDO!</h1>
      <p>
        Eu sou a Liv, sua assistente virtual! Estou aqui para te ajudar a
        entender seu consumidor!
      </p>
      <button @click="startChat">Clique aqui para começar!</button>
    </div>
    <div class="sub">
      <p>@DOM ROCK 2024</p>
    </div>
  </div>
</div>
</template>
  
  <script>
export default {
  methods: {
    startChat() {
      this.$emit("start-chat");
    },
  },
};
</script>


  
  <style scoped>

.fundo{
  width: 100vw;
  height: 100vh;
  background-color: black;
  position: absolute;
}



.home {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  height: 100vh;
  background-image: url("@/assets/fundo.png");
  background-size: cover;
  background-position: center;


}

.logo {
  background-image: url("@/assets/logo.psd.png");
  background-size: cover;
  background-position: center;
  width: 120px;
  height: 60px;
}

.dom {
  background-image: url("@/assets/dom.png");
  background-size: cover;
  background-position: center;
  width: 120px;
  height: 60px;
}

.mais {
  background-image: url("@/assets/mais.png");
  background-size: cover;
  background-position: center;
  width: 40px;
  height: 60px;
}

.sub {
  position: absolute;
  bottom: 0;
  color: white;
  font-weight: 300;
  font-family: "IBM Plex Mono", monospace;
}

h1 {
  font-family: "Oswald", sans-serif;
  font-weight: 400;
  font-size: 38px;
}

.bloco {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: 20px;
  height: 40vh;
  width: 40vw;
  border-radius: 20px;
  background-color: rgba(0, 0, 0, 0.4);
  color: aliceblue;
  font-family: "IBM Plex Mono", sans-serif;
  font-weight: 500;
  text-align: center;
  font-size: 18px;
}

p {
  padding: 0px 25px;
}

button {
  padding: 5px 20px;
  font-size: 16px;
  cursor: pointer;
  width: 60%;
  height: 40px;
  border-radius: 20px;
  border: none;
  transition: all 0.2s ease-in-out;
  font-size: 20px;
  font-family: "Oswald", sans-serif;
  margin-top: 20px;
  color: black;
}

button:hover {
  background-color: rgb(138, 160, 160);
}


.imagens{
    display: flex;
    justify-content: center;
    align-items: center;
    width: 350px;
    transform: translate(0px, -100px);
}

</style>
  