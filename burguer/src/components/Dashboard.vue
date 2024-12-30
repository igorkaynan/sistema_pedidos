<template>
    <div id="burger-table">
      <Message :msg="msg" v-show="msg" />
      <div>
        <div id="burger-table-heading">
          <div class="order-id">#:</div>
          <div>Cliente:</div>
          <div>Pão:</div>
          <div>Carne:</div>
          <div>Opcionais:</div>
          <div>Ações:</div>
        </div>
      </div>
      <div id="burger-table-rows">
        <div class="burger-table-row" v-for="burger in burgers" :key="burger.id">
          <div class="order-number">{{ burger.id }}</div>
          <div>{{ burger.nome }}</div>
          <div>{{ burger.pao }}</div>
          <div>{{ burger.carne }}</div>
          <div>
            <ul>
              <li v-for="(opcional,index) in burger.opcionais" :key="index">
                {{ opcional }}
              </li>
            </ul>
          </div>
          <div class="actions">
            <select name="status" class="status" @change="updatedBurger($event,burger.id)">
              <option value="">Selecione</option>
              <option v-for="s in status"  :key="s.id" :value="s.tipo" :selected="burger.status == s.tipo">
                {{ s.tipo }}
              </option>
            </select>
            <button class="delete-btn" @click="deleteBurger (burger.id)">Cancelar</button>
          </div>
        </div>
      </div>

    </div>
  </template>
  
  <script>
import Message from './Message.vue';

  export default {
    name: "Dashboard",
    data(){
      return {
        burgers: null,
        burger_id: null,
        status: [],
        msg: null
      }
    },
    components:{
      Message
    },

    methods:{
      async getPedidos(){
        const req = await fetch("http://localhost:3000/burgers")

        const data = await req.json();

        this.burgers = data;

        //resgatar os status
        this.getStatus();

      },
      async getStatus(){
        const req = await fetch("http://localhost:3000/status");
     
        const data = await req.json();

        this.status = data;

        console.log(data);
      },
      async deleteBurger(id){

        const req = await fetch(`http://localhost:3000/burgers/${id}`,{
          method: "DELETE"
        });

        const res = await req.json();

        
// msg

//colocar uma msg de sistema
this.msg = `Pedido Removido com sucesso!`;

//limpar msg
setTimeout(() => this.msg = "", 3000);

//msg



        this.getPedidos();

      },
      async updatedBurger(event,id){

        const option =  event.target.value;

        const dataJson = JSON.stringify({ status: option });

        const req = await fetch(`http://localhost:3000/burgers/${id}`,{
          method: "PATCH",
          headers: { "Content-Type": "application/json" },
          body: dataJson
        });

        const res = await req.json();



// msg

//colocar uma msg de sistema
this.msg = `O Pedido N°${res.id} foi atualizado para ${res.status}!`;

//limpar msg
setTimeout(() => this.msg = "", 3000);

//msg




        console.log(res);

      }
    },
    mounted(){
      this.getPedidos();
    }
  }
  </script>
  
  <style scoped>
  #burger-table {
    max-width: 1200px;
    margin: 0 auto;
  }
  
  #burger-table-heading,
  #burger-table-rows {
    display: flex;
    flex-wrap: wrap;
  }
  
  #burger-table-heading {
    font-weight: bold;
    padding: 12px;
    border-bottom: 3px solid #333;
    background-color: #f4f4f4;
  }
  
  .burger-table-row {
    width: 100%;
    display: flex;
    padding: 12px;
    border: 1px solid #ccc;
    margin-bottom: 10px;
    align-items: center;
    background-color: #fff;
  }
  
  #burger-table-heading div,
  .burger-table-row div {
    padding: 0 10px;
    text-align: left;
  }
  
  #burger-table-heading .order-id,
  .burger-table-row .order-number {
    width: 5%;
  }
  
  #burger-table-heading div:not(.order-id),
  .burger-table-row div:not(.order-number) {
    width: 15%;
  }
  
  ul {
    margin: 0;
    padding-left: 20px;
  }
  
  select {
    padding: 6px 12px;
    margin-right: 12px;
    height: 36px; /* Garantir que o select tenha uma altura consistente */
    width: 100%; /* Faz com que o select ocupe o espaço disponível */
  }
  
  .delete-btn {
    background-color: #222;
    color: #FCBA03;
    font-weight: bold;
    border: 2px solid #222;
    padding: 10px;
    font-size: 16px;
    cursor: pointer;
    transition: 0.5s;
    height: 36px; /* Mesma altura do select */
    line-height: 1; /* Ajuste da altura da linha */
    width: auto; /* Permite que o botão tenha o tamanho correto */
    margin-left: 12px; /* Espaço entre o botão e o select */
  }
  
  .delete-btn:hover {
    background-color: transparent;
    color: #222;
  }
  
  .actions {
    display: flex;
    align-items: center; /* Alinha o select e o botão verticalmente */
    justify-content: space-between; /* Distribui o conteúdo da célula de forma justa */
    width: 100%; /* Faz com que a coluna ocupe todo o espaço disponível */
  }
  
  .burger-table-row div:last-child {
    display: flex;
    flex-grow: 1; /* Garante que o "Ações" ocupe todo o espaço disponível */
    justify-content: flex-end; /* Alinha os elementos no final da célula */
  }
  </style>