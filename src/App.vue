<template>
  <div class="app">
    <header>
      <div class="logo">
        <img src="@/assets/logo.svg" alt="BookFinder" />
      </div>
      <div class="search-bar">
        <input type="text" v-model="searchTerm" placeholder="Digite o título/autor do livro e aperte Enter"
          @keydown.enter="searchBooks" />
        <button v-if="searchTerm" class="clear-button" @click="clearSearchTerm">
          <span>&times;</span>
        </button>
        <button class="search-button" @click="searchBooks">Buscar</button>
      </div>
    </header>
    <main>
      <div v-if="isLoading" class="loader">
        <div class="spinner"></div>
      </div>
      <div class="cards">
        <div v-for="book in books" :key="book.id" class="card" @click="openBookLink(book.link)">
          <img :src="book.thumbnail" :alt="book.title" />
          <h3>{{ book.title }}</h3>
          <p>{{ book.authors.join(', ') }}</p>
        </div>
        <div v-if="!isLoading && !books.length" class="no-results">
          Nenhum livro encontrado.
        </div>
      </div>
    </main>
    <footer>
      <p>&copy; 2023 BookFinder. Todos os direitos reservados.</p>
    </footer>
  </div>
</template>

<script>
export default {
  data() {
    return {
      searchTerm: '',
      books: [],
      isLoading: false
    };
  },
  methods: {
    searchBooks() {
      if (this.searchTerm) {
        this.isLoading = true;
        fetch(`https://www.googleapis.com/books/v1/volumes?q=${encodeURIComponent(this.searchTerm)}`)
          .then(response => response.json())
          .then(data => {
            if (data.items) {
              this.books = data.items.map(item => ({
                id: item.id,
                title: item.volumeInfo.title,
                authors: item.volumeInfo.authors || [],
                thumbnail: item.volumeInfo.imageLinks ? item.volumeInfo.imageLinks.thumbnail : '',
                link: item.volumeInfo.infoLink
              }));
            } else {
              this.books = [];
            }
            this.isLoading = false;
          })
          .catch(error => {
            console.error(error);
            this.books = [];
            this.isLoading = false;
          });
      } else {
        this.books = [];
      }
    },
    openBookLink(link) {
      window.open(link, '_blank');
    },
    clearSearchTerm() {
      this.searchTerm = '';
    }
  }
};
</script>

<style>
/* Estilos gerais */
* {
  box-sizing: border-box;
}

body {
  margin: 0;
  font-family: Arial, sans-serif;
  background-color: #f7f7f7;
}

/* Estilos específicos para o componente */
.app {
  max-width: 1200px;
  margin: 0 auto;
  display: flex;
  flex-direction: column;
  min-height: 100vh;
}

header {
  background-color: #f2f2f2;
  padding: 20px;
  display: flex;
  align-items: center;
  flex-wrap: wrap;
  justify-content: space-between;
}

.logo {
  margin-right: 20px;
}

.logo img {
  max-height: 40px;
}

.search-bar {
  position: relative;
  flex-grow: 1;
  display: flex;
  align-items: center;
  gap: 10px;
}

input[type="text"] {
  padding: 10px;
  font-size: 16px;
  width: 100%;
  border: none;
  border-radius: 20px;
  padding-right: 40px;
  padding-left: 20px;
  background-color: #fff;
  outline: none;
}

.clear-button {
  position: absolute;
  top: 50%;
  right: 115px;
  transform: translateY(-50%);
  background: none;
  border: none;
  cursor: pointer;
}

.clear-button span {
  font-size: 20px;
}

.search-button {
  background-color: #4caf50;
  color: #fff;
  border: none;
  border-radius: 20px;
  padding: 10px 20px;
  font-size: 16px;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.search-button:hover {
  background-color: #45a049;
}

main {
  flex: 1;
  padding: 20px;
  display: flex;
  justify-content: center;
  align-items: center;
}

.loader {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
}

.spinner {
  border: 4px solid rgba(0, 0, 0, 0.1);
  border-left-color: #4caf50;
  animation: spin 1s linear infinite;
  border-radius: 50%;
  width: 50px;
  height: 50px;
}

@keyframes spin {
  to {
    transform: rotate(360deg);
  }
}

.cards {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
  gap: 20px;
  max-width: 1200px;
  width: 100%;
  margin: 0 auto;
}

.card {
  background-color: #fff;
  padding: 20px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  text-align: center;
  cursor: pointer;
  transition: transform 0.3s ease;
}

.card:hover {
  transform: translateY(-5px);
}

.card img {
  max-width: 100%;
  height: auto;
  margin-bottom: 10px;
}

.card h3 {
  margin-top: 0;
}

.no-results {
  text-align: center;
}

footer {
  background-color: #f2f2f2;
  padding: 20px;
  text-align: center;
  margin-top: auto;
}
</style>
