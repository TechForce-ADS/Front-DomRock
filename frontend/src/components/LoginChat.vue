<template>
  <div class="fundo">
    <div class="home">
      <div class="imagens">
        <div class="dom"></div>
        <div class="mais"></div>
        <div class="logo"></div>
      </div>
      <div class="bloco">
        <h1>Login</h1>
        <form @submit.prevent="handleLogin" class="formLogin">
    
          <input class="LoginInput"
            type="email"
            v-model="email"
            placeholder="Digite seu e-mail"
            required
          />
      
          <input class="LoginInput"
            type="password"
            v-model="password"
            placeholder="Digite sua senha"
            required
          />
          <button type="submit">Entrar</button>
        </form>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "LoginChat",
  data() {
    return {
      email: "",
      password: "",
    };
  },
  methods: {
    handleLogin() {
      if (this.email && this.password) {
        console.log(`Login realizado com: ${this.email}`);
        this.$emit("login-success");
      } else {
        alert("Por favor, preencha todos os campos.");
      }
    },
  },
};
</script>

<style scoped>
.fundo {
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

.imagens {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 350px;
  transform: translate(0px, -100px);
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

.logo {
  background-image: url("@/assets/logo.psd.png");
  background-size: cover;
  background-position: center;
  width: 120px;
  height: 60px;
}

.titulo h1 {
  color: white;
  font-family: "Oswald", sans-serif;
  font-size: 32px;
  margin-bottom: 10px;
}

.titulo p {
  color: aliceblue;
  font-family: "IBM Plex Mono", monospace;
  font-size: 18px;
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

button {
  background-color: white;
  color: black;
  font-weight: bold;
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
}

button:hover {
  background-color: #3cb371;
  color: white;
}

.formLogin {
  width: 50%;

  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  gap:20px
}

.LoginInput {
  width: 100%; 
  height: 40px; 
  padding: 10px; 
  margin-bottom: 10px; 
  border: 1px solid #ccc;
  border-radius: 18px; 
  font-size: 16px; 
  font-family: "IBM Plex Mono", monospace;
  outline: none;
  background-color: rgba(0, 0, 0, 0);
  color: white;
}



</style>
