<template>
  <div id="app">
    <transition name="fade-slide">
      <!-- Exibe a tela de LoginChat primeiro -->
      <LoginChat v-if="!isLoggedIn" @login-success="handleLoginSuccess" />
      <!-- Exibe a tela inicial após o login -->
      <Home v-else-if="!chatStarted" @start-chat="chatStarted = true" />
      <!-- Exibe o chatbot quando o chat começa -->
      <ChatBot v-else />
    </transition>
  </div>
</template>

<script>
import Home from "./components/HomeChat.vue";
import ChatBot from "./components/ChatBot.vue";
import LoginChat from "./components/LoginChat.vue";

export default {
  components: {
    Home,
    ChatBot,
    LoginChat,
  },
  data() {
    return {
      chatStarted: false, // Controla se o chat foi iniciado
      isLoggedIn: false,  // Controla se o usuário está logado
    };
  },
  methods: {
    handleLoginSuccess() {
      // Marca o login como concluído
      this.isLoggedIn = true;
    },
  },
};
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Oswald:wght@300;400;500;600;700&display=swap');
@import url('https://fonts.googleapis.com/css2?family=IBM+Plex+Mono:ital,wght@0,100;0,200;0,300;0,400;0,500;0,600;0,700;1,100;1,200;1,300;1,400;1,500;1,600;1,700&family=Plus+Jakarta+Sans:ital,wght@0,200..800;1,200..800&display=swap');
@import url('https://fonts.googleapis.com/css2?family=IBM+Plex+Mono:ital,wght@0,100;0,200;0,300;0,400;0,500;0,600;0,700;1,100;1,200;1,300;1,400;1,500;1,600;1,700&family=Plus+Jakarta+Sans:ital,wght@0,200..800&family=Quicksand:wght@300..700&display=swap');

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

/* Transição de fade com slide */
.fade-slide-enter-active, .fade-slide-leave-active {
  transition: all 1s ease-in-out;
}

.fade-slide-enter, .fade-slide-leave-to {
  transform: translateY(100%);
}
</style>
