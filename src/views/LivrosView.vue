<script>
import LivrosApi from "../api/livros";
const livros = new LivrosApi();
  import axios from "axios";
  export default {
    data() {
      return {
        livros: [],
        categorias: [],
        autores: [],
        editoras: [],
        livro: {},
      };
    },
    async created() {
      await this.BuscarTodasAsEditoras();
      await this.BuscarTodasAsCategorias();
      await this.BuscarTodosOsAutores();
      const livros = await axios.get(
        `https://livraria-larievini.herokuapp.com/livros?expand=categoria&expand=editora&expand=autor`
      );
      this.livros = livros.data;
    },
    methods: {
      async BuscarTodasAsEditoras() {
        const editoras = await axios.get(`https://livraria-larievini.herokuapp.com/editoras`);
        this.editoras = editoras.data;
      },
      async BuscarTodasAsCategorias() {
        const categorias = await axios.get(`https://livraria-larievini.herokuapp.com/categorias`);
        this.categorias = categorias.data;
      },
      async BuscarTodosOsAutores() {
        const autores = await axios.get(`https://livraria-larievini.herokuapp.com/autores`);
        this.autores = autores.data;
      },
      async salvar() {
        const livro_criado = await axios.post(
          `https://livraria-larievini.herokuapp.com/livros`,
          this.livro
        );
        this.livros.push(livro_criado.data);
        this.livro = {};
        const livros = await axios.get(
          `https://livraria-larievini.herokuapp.com/livros?expand=categoria&expand=editora&expand=autor`
        );
        this.livros = livros.data;
      },
      async excluir(livro) {
        await axios.delete(`https://livraria-larievini.herokuapp.com/livros/${livro.id}`);
        const indice = this.livros.indexOf(livro);
        this.livros.splice(indice, 1);
      },
    },
  };
  </script>
  <template>
    <div class="container">
      <div class="title">
        <h2>Gerenciamento de livros</h2>
      </div>
      <div class="form-input">
        <input type="text" v-model="livro.nome" placeholder="Nome" />
  
        <select v-model="livro.categoriaId">
          <option
            v-for="categoria in categorias"
            :key="categoria.id"
            :value="categoria.id"
          >
            {{ categoria.nome }}
          </option>
        </select>
  
        <select v-model="livro.editoraId">
          <option
            v-for="editora in editoras"
            :key="editora.id"
            :value="editora.id"
          >
            {{ editora.nome }}
          </option>
        </select>
  
        <select v-model="livro.autorId">
          <option v-for="autor in autores" :key="autor.id" :value="autor.id">
            {{ autor.nome }}
          </option>
        </select>
  
        <button @click="salvar">Enviar</button>
      </div>
    </div>
    <div class="list-items">
      <table class="w-100">
        <thead>
          <tr>
            <th>ID</th>
            <th>Livro</th>
            <th>Categoria</th>
            <th>Editora</th>
            <th>Autor</th>
            <th>Ações</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="livro in livros" :key="livro.id">
            <td>{{ livro.id }}</td>
            <td>{{ livro.nome }}</td>
            <td>{{ livro.categoriaId }}</td>
            <td>{{ livro.editoraId }}</td>
            <td>{{ livro.autorId }}</td>
            <td>
              <button @click="excluir(livro)">Apagar</button>
              <button @click="editar(livro)">Editar</button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </template>
  