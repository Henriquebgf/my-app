<template>
  <v-container>
    <v-row class="text-center">
      <v-col cols="12">
        <h1>Lista de alunos</h1>
      </v-col>
    </v-row>
    <v-row class="text-center">
      <v-col cols="12">
         <v-data-table
          :headers="headers"
          :items="alunos"
          item-key="id"
          class="elevation-1"
        >
          <template v-slot:top>
            <v-toolbar flat>
              <v-toolbar-title> Alunos</v-toolbar-title>
              <v-divider class="mx-4" inset vertical></v-divider>
              <v-spacer></v-spacer>
              <v-dialog v-model="dialog" max-width="800px">
                <template v-slot:activator="{ on, attrs }">
                  <v-btn
                    color="primary"
                    dark
                    class="mb-2"
                    v-bind="attrs"
                    v-on="on"
                  >
                    Novo Aluno
                  </v-btn>
                </template>
                <v-card>
                  <v-card-title>
                    <span class="headline">{{ formTitle }}</span>
                  </v-card-title>

                  <v-card-text>
                    <v-container>
                      <v-row>
                        <v-col cols="12" sm="6" md="8">
                          <v-text-field
                            v-model="editedItem.nome"
                            label="Nome:"
                          ></v-text-field>
                        </v-col>
                        <v-col cols="12" sm="6" md="4">
                          <v-text-field
                            v-model="editedItem.nota"
                            label="Nota:"
                          ></v-text-field>
                        </v-col>
                      </v-row>
                      <v-row>
                        <v-col cols="12" sm="6" md="2">
                          <v-text-field
                            v-model="editedItem.id"
                            label=" ID"
                          ></v-text-field>
                        </v-col>

                        <v-col cols="12" sm="6" md="6">
                          <v-text-field
                            v-model="editedItem.curso"
                            label="Curso:"
                          ></v-text-field>
                        </v-col>

                        <v-col cols="12" sm="6" md="4">
                          <v-text-field
                            v-model="editedItem.turno"
                            label="Turno:"
                          ></v-text-field>
                        </v-col>
                      </v-row>
                    </v-container>
                  </v-card-text>

                  <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn color="blue darken-1" text @click="close">
                      Cancelar
                    </v-btn>
                    <v-btn color="blue darken-1" text @click="save">
                      Salvar
                    </v-btn>
                  </v-card-actions>
                </v-card>
              </v-dialog>
            </v-toolbar>
          </template>
        <template v-slot:[`item.actions`]="{ item }">
            <v-icon small class="mr-2" @click="editItem(item)"> mdi-pencil </v-icon>
            <v-icon small @click="deleteItem(item)"> mdi-delete </v-icon>
        </template>
        <template v-slot:no-data>
        <v-btn color="primary" @click="inicializa"> Reset </v-btn>
        </template>
        </v-data-table>
      </v-col>
    </v-row>
  </v-container>
</template>



<script>
import axios from "axios";
export default {
  name: "AlunoCrud",
  data(){
    return{
    search: "",
    dialog: false,
    headers: [
        { text: "#", value: "id" },
        { text: "Nome", value: "nome" },
        { text: "Curso", value: "curso" },
        { text: "Nota", value: "nota" },
        { text: "Turno", value: "turno" },
        { text: "Actions", value: "actions", sortable: false },
      ],
    alunos: [],
    editedIndex: -1,
    editedItem: {
        id: 0,
        nome: "",
        curso: "",
        nota: 0,
        turno: "",
    },
    defaultItem: {
        id: 0,
        nome: "",
        curso: "",
        nota: 0,
        turno: "",
    },
  };
},
  methods: {
    inicializa() {
      axios("http://localhost:3000/alunos")
        .then((response) => {
          this.alunos = response.data;
        })
        .catch((error) => console.log(error));
    },
    close() {
      this.dialog = false;
      setTimeout(() => {
        this.editedItem = Object.assign({}, this.defaultItem);
        this.editedIndex = -1;
      }, 300);
    },
    save() {
      if (this.editedIndex > -1) {
        //alteracao
        axios
          .put(
            "http://localhost:3000/alunos/" + this.editedItem.id,
            this.editedItem
          )
          .then((response) => {
            console.log(response);
            Object.assign(this.alunos[this.editedIndex], this.editedItem);
            this.close();
          })
          .catch((error) => console.log(error));
      } else {
        //Inclusao
        axios
          .post("http://localhost:3000/alunos", this.editedItem)
          .then((response) => {
            console.log(response);
            this.alunos.push(this.editedItem);
            this.close();
          })
          .catch((error) => console.log(error));
      }
    },
    editItem(item) {
      this.editedIndex = this.alunos.indexOf(item);
      this.editedItem = Object.assign({}, item);
      this.dialog = true;
    },
    deleteItem(item) {
      const index = this.alunos.indexOf(item);
      confirm("Deseja apagar este item?") &&
        axios
          .delete("http://localhost:3000/alunos/" + item.id)
          .then((response) => {
            console.log(response.data);
            this.alunos.splice(index, 1);
          })
          .catch((error) => console.log(error));
    },
  },
  computed: {
    formTitle() {
      return this.editedIndex === -1 ? "Novo Item" : "Editar Item";
    },
  },
  watch: {
    dialog(val) {
      val || this.close();
    },
    dialogDelete(val) {
      val || this.closeDelete();
    },
  },
  created() {
    this.inicializa();
  },
};
</script>

