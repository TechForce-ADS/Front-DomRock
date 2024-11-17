<template>
  <div class="fundo">
    <div class="logo"></div>
    <h2>Como posso ajudar?</h2>
    <h5>Sugestões de perguntas:</h5>
    <div class="sugestoes">
      <button
        @click="setUserQuery('Me recomende notebooks')"
        class="btn-sugestao"
      >
        Me recomende notebooks
      </button>
      <button
        @click="setUserQuery('Me mostre avaliações de um bom celular')"
        class="btn-sugestao"
      >
        Me mostre avaliações de um bom celular
      </button>
      <button
        @click="setUserQuery('Me fale sobre um produto especifico')"
        class="btn-sugestao"
      >
        Me fale sobre um produto especifico
      </button>
    </div>

   
    
    <div class="chat">
      <div
        class="mensagem"
        v-for="msg in messages"
        :key="msg.id"
        :class="{
          'liv-message': msg.role === 'Liv',
          'user-message': msg.role === 'User',
        }"
      >
        <strong>{{ msg.role }}:</strong> {{ msg.message }}
        <!-- Botão de copiar -->
        <button
          v-if="msg.role === 'Liv'"
          class="copy-btn"
          @click="copyToClipboard(msg)"
          title="Copiar"
        >
          <i :class="msg.iconState"></i>
        </button>
      </div>
      <!-- Exibe "..." enquanto a resposta está sendo gerada -->
      <div v-if="loading" class="loading-indicator liv-message mensagem">...</div>
    </div>
    <div class="bloco">
      <input
        v-model="userQuery"
        @keyup.enter="sendMessage"
        placeholder="Digite sua pergunta..."
        class="pergunta"
      />
      <button @click="sendMessage" class="btn-enviar"></button>
    </div>
  </div>
</template>

<script>
  import axios from "axios";

  export default {
    data() {
      return {
        userQuery: "",
        messages: [],
        loading: false, // Controle de carregamento
      };
    },
    methods: {
      async sendMessage() {
        if (this.userQuery.trim() === "") return;

        // Adiciona a mensagem do usuário
        this.messages.push({ role: "User", message: this.userQuery });
        this.loading = true; // Ativa o indicador de carregamento

        try {
          const response = await axios.post("http://127.0.0.1:5000/ask", {
            query: this.userQuery,
          });

          // Adiciona a resposta do chatbot com o estado inicial do ícone
          this.messages.push({
            role: "Liv",
            message: response.data.response,
            iconState: "icon-copy", // Estado inicial do ícone de cópia
          });
        } catch (error) {
          console.error("Erro ao se comunicar com o servidor:", error);
        } finally {
          this.loading = false; // Desativa o indicador de carregamento
        }

        // Limpa o campo de input
        this.userQuery = "";
      },

      setUserQuery(query) {
        this.userQuery = query; // Define a consulta do usuário
      },

      copyToClipboard(msg) {
        msg.iconState = "icon-check"; // Altera o ícone apenas para a mensagem clicada
        navigator.clipboard.writeText(msg.message)
          .then(() => {
            setTimeout(() => {
              msg.iconState = "icon-copy"; // Reverte o ícone após 1 segundo
            }, 2000);
          })
          .catch(err => {
            console.error("Erro ao copiar texto:", err);
          });
      },
    },
  };
</script>

<style scoped>
.fundo {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  height: 100vh;
  background-color: black;
}

.logo {
  background-image: url("@/assets/logo.psd.png");
  background-size: cover;
  background-position: center;
  width: 120px;
  height: 60px;
  margin-bottom: 8%;
}

.pergunta {
  width: 100%;
  height: 100%;
  border-radius: 20px;
  border: none;
  outline: none;
  padding: 20px;
  box-sizing: border-box;
  color: white;
  background-color: #21232a;
  font-family: "Quicksand", sans-serif;
 
}

.pergunta::placeholder {
  color: white;
  font-family: "Quicksand", sans-serif;
}

.btn-enviar {
  width: 100px;
  position: relative;
  left: 0;
  height: 100px;
  border-radius: 50px;
  background-image: url("@/assets/enviar.png");
  background-size: cover;
  background-position: center;
  border: none;
  cursor: pointer;
  background-color: rgba(0, 0, 0, 0);
  transition: all 0.2s ease-in-out;
}

.btn-enviar:hover {
  transform: scale(1.1) ;
}

.bloco {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 20px;
  height: 5vh;
  width: 30vw;
  color: aliceblue;
  font-family: "IBM Plex Mono", sans-serif;
  text-align: center;
  font-size: 18px;
}

h2 {
  color: rgb(255, 255, 255);
  font-family: "Quicksand", sans-serif;
  font-weight: 200;
  font-size: 32px;
  margin-bottom: 30px;
}

.chat {
  width: 40%;
  height: 40%;
  overflow-x: auto;
  margin-top: 3%;
}

/* Estilos para a barra de rolagem */
.chat::-webkit-scrollbar {
  width: 8px; /* Largura da barra de rolagem */
}

.chat::-webkit-scrollbar-track {
  background: #21232a; /* Cor de fundo do track */
  border-radius: 10px;
}

.chat::-webkit-scrollbar-thumb {
  background-color: #444; /* Cor do "polegar" da barra de rolagem */
  border-radius: 10px; /* Arredondamento do "polegar" */
  border: 2px solid #21232a; /* Margem para um efeito "afundado" */
}

.chat::-webkit-scrollbar-thumb:hover {
  background-color: #555; /* Cor do "polegar" quando em hover */
}

.mensagem {
  width: 80%;
  padding: 10px;
  border-radius: 10px;
  font-family: "Quicksand", sans-serif;
  white-space: pre-wrap;
  margin-bottom: 10px;
  color: aliceblue;
}

.user-message {
  align-self: flex-start;
  background-color: rgba(255, 255, 255, 0.322);
  text-align: left;
}

.liv-message {
  align-self: flex-end;
  background-color: rgba(50, 50, 50, 0.274);
  text-align: left;
  margin-left: 15%;
}

.btn-sugestao {
  width: auto;
  padding: 10px;
  border-radius: 20px;
  background-color: #21232aa6;
  border: none;
  margin: 10px;
  color: rgb(216, 202, 202);
  font-family: "Quicksand", sans-serif;
  cursor: pointer;
  transition: all 0.2s ease-in-out;
  margin-bottom: 20px;
}



.btn-sugestao:hover {
  transform: scale(1.1);
}

h5{
  color: rgb(255, 255, 255);
  font-family: "Quicksand", sans-serif;
  font-weight: 100;
  font-size: 16px;
  
}

.loading-indicator {
  color: aliceblue;
  font-family: "Quicksand", sans-serif;
  font-size: 30px;
  animation: dots 1s steps(5, end) infinite;
  margin-top: 10px;
}

/* Animação de "..." */
@keyframes dots {
  0%, 20% { content: "."; }
  40% { content: ".."; }
  60% { content: "..."; }
  80%, 100% { content: ""; }
}

.copy-btn {
  background: none;
  border: none;
  cursor: pointer;
  margin-top: 12px;

}

.copy-btn:hover {
  transform: scale(1.1);
}

.icon-copy {
  display: inline-block;
  width: 20px;
  height: 20px;
  background: url("@/assets/copiar.png") no-repeat center;
  background-size: contain;
}

.icon-check {
  display: inline-block;
  width: 20px;
  height: 20px;
  background: url("@/assets/copiado.png") no-repeat center;
  background-size: contain;
}

</style>