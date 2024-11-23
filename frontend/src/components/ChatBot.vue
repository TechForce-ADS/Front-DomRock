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

    <div class="bloco">
      <input
        v-model="userQuery"
        @keyup.enter="sendMessage"
        placeholder="Digite sua pergunta..."
        class="pergunta"
      />
      <button @click="sendMessage" class="btn-enviar"></button>
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
        <template v-if="editingIndex === msg.id">
          <textarea 
            v-model="editQuery" 
            class="edit-textarea"
          ></textarea>
          <div class="edit-buttons">
            <button @click="saveEdit" class="btn-edit-save">Salvar</button>
            <button @click="cancelEdit" class="btn-edit-cancel">Cancelar</button>
          </div>
        </template>

        <template v-else>
          <!-- Exibe mensagem -->
          <strong>{{ msg.role }}:</strong> {{ msg.message }}
          <!-- Botão de editar -->
          <button
            v-if="msg.role === 'User'"
            class="icon-edit"
            title="Editar mensagem"
            @click="startEdit(msg.id, msg.message)"
          ></button>

          <!-- Botão de copiar -->
          <button
            v-if="msg.role === 'Liv'"
            class="copy-btn"
            @click="copyToClipboard(msg)"
            title="Copiar"
          >
            <i :class="msg.iconState"></i>
          </button>
        </template>
      </div>

      <!-- Exibe "..." enquanto uma resposta nova está sendo carregada -->
      <div v-if="loading && editingIndex === null" class="loading-indicator liv-message mensagem">...</div>
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
      editingIndex: null, // Índice da mensagem sendo editada
      editQuery: "", // Texto temporário durante a edição
    };
  },
  methods: {
    async sendMessage() {
      if (this.userQuery.trim() === "") return;

      // Adiciona a mensagem do usuário com um ID único
      this.messages.push({
        id: Date.now(), // Gera um ID único baseado no timestamp
        role: "User",
        message: this.userQuery,
      });

      this.loading = true; // Ativa o indicador de carregamento

      try {
        const response = await axios.post("http://127.0.0.1:5000/ask", {
          query: this.userQuery,
        });

        // Adiciona a resposta do chatbot com um ID único
        this.messages.push({
          id: Date.now() + 1, // Gera outro ID único
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

    startEdit(id, message) {
      this.editingIndex = id; // Usa o ID para identificar a mensagem em edição
      this.editQuery = message;
    },

    async saveEdit() {
      if (this.editQuery.trim() === "") return;

      // Localiza a mensagem no array pelo id
      const messageIndex = this.messages.findIndex(
        (msg) => msg.id === this.editingIndex
      );

      if (messageIndex !== -1) {
        // Atualiza a mensagem no array
        this.messages[messageIndex].message = this.editQuery;

        // (opcional) Reenvia ao servidor, se necessário
        this.loading = true;
        try {
          const response = await axios.post("http://127.0.0.1:5000/ask", {
            query: this.editQuery,
          });

          // Localiza a resposta correspondente e atualiza
          const responseIndex = this.messages.findIndex(
            (msg, i) => msg.role === "Liv" && i > messageIndex
          );

          if (responseIndex !== -1) {
            this.messages[responseIndex].message = response.data.response;
          } else {
            this.messages.push({
              id: Date.now(),
              role: "Liv",
              message: response.data.response,
              iconState: "icon-copy",
            });
          }
        } catch (error) {
          console.error("Erro ao atualizar a resposta:", error);
        } finally {
          this.loading = false;
          this.editingIndex = null;
          this.editQuery = "";
        }
      }
    },

    cancelEdit() {
      this.editingIndex = null;
      this.editQuery = "";
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
  transform: scale(1.1);
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

.chat {
  width: 40%;
  height: 40%;
  overflow-x: auto;
  margin-top: 3%;
}

.chat::-webkit-scrollbar {
  width: 8px;
}

.chat::-webkit-scrollbar-track {
  background: #21232a;
  border-radius: 10px;
}

.chat::-webkit-scrollbar-thumb {
  background-color: #444;
  border-radius: 10px;
  border: 2px solid #21232a;
}

.chat::-webkit-scrollbar-thumb:hover {
  background-color: #555;
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
  background-color: rgba(50, 50, 50, 0.274);
  align-self: flex-end;
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
}

.btn-sugestao:hover {
  transform: scale(1.1);
}

h5 {
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

.edit-textarea {
  width: 100%;
  border: none;
  border-radius: 10px;
  padding: 10px;
  font-size: 14px;
  font-family: "Quicksand", sans-serif;
  background-color: #21232a;
  color: white;
  resize: none;
  margin-top: 10px; /* Espaço entre os botões */
}

.icon-edit {
  width: 22px;
  height: 22px;
  background: url("@/assets/editar.png") no-repeat center;
  background-size: contain;
  cursor: pointer;
  margin-left: 10px;
  border: none;
  background-color: transparent;
  padding: 0;
  outline: none;
  display: inline-block;
  vertical-align: middle; /* Alinha o ícone ao meio verticalmente */
}

.icon-edit:hover {
  transform: scale(1.1); /* Adiciona efeito de hover */
  transition: 0.2s ease-in-out;
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

/* Estilo para o botão "Cancelar" */
.btn-edit-cancel {
  background-color: #121212; /* Preto mais escuro */
  color: white; /* Texto branco */
  border: none;
  border-radius: 20px; /* Botão arredondado */
  padding: 10px;
  font-size: 14px;
  cursor: pointer;
  transition: all 0.2s ease-in-out;
  margin-top: 10px; /* Espaço entre os botões */
}

.btn-edit-cancel:hover {
  background-color: #1c1c1c; /* Um tom mais claro no hover */
  transform: scale(1.1); /* Efeito de escala */
}

/* Estilo para o botão "Salvar" */
.btn-edit-save {
  background-color: white; /* Fundo branco */
  color: black; /* Texto preto */
  border: none;
  border-radius: 20px; /* Botão arredondado */
  padding: 10px;
  font-size: 14px;
  cursor: pointer;
  transition: all 0.2s ease-in-out;
  margin-right: 10px; /* Espaço entre os botões */
  margin-top: 10px; /* Espaço entre os botões */
}

.btn-edit-save:hover {
  background-color: #f2f2f2; /* Um tom cinza claro no hover */
  transform: scale(1.1); /* Efeito de escala */
}

</style>
