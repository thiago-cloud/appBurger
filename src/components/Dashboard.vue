<template>
    <div id="burger-table">
        <div>
          <Message :msg="msg" v-show="msg"/>
            <div id="burger-table-heading">
                <div class="order-id">#</div>
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
                    <ul v-for="(opcional, index) in burger.opcionais" :key="index">
                        <li>
                            {{ opcional }}
                        </li>
                    </ul>
                </div>
                <div>
                    <select name="status" class="status" @change="updateBurger($event, burger.id)">
                        <option value="">Selecione</option>
                        <option  v-for="statu in status" :key="statu.id" :value="statu.tipo" :selected="burger.status == statu.tipo">
                          {{ statu.tipo }}
                        </option>
                    </select>
                    <button class="delete-btn" @click="deleteBurger(burger.id)">Cancelar</button>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
import Message from './Message.vue';

    export default{
        name: 'Dashboard',
        components:{
          Message,
        },
        data(){
            return {
                burgers: null,
                burger_id: null,
                status: [],
                msg: null,
            }
        },
        methods:{

            //Requsição dos pedidos
            async getPedidos(){
              const req = await fetch("http://localhost:3000/burgers");

              const data = await req.json()

              this.burgers = data;

              //resgatar os stauts
              this.getStatus();

            },

            //Requsição dos status dos pedidos
            async getStatus(){
              const req = await fetch("http://localhost:3000/status");

              //Ao receber a requisição do tipo json então coloque de dentro do parametro value e passe para status
              req.json().then(value => {
                this.status = value;
              });

            },

            //Delete dos pedidos
            async deleteBurger(id){
              console.log(id)
              
              //Essa função de delete é exclusiva do json-server
              const req = await fetch(`http://localhost:3000/burgers/${id}`, {
                method:"DELETE"
              })
              const res = await req.json()

              this.msg = `Pedido removido com sucesso!`

              //Limpar msg
              setTimeout(()=> {
                this.msg = ""
              }, 5000)

              //Mostrara o resultado novamente sem o pedido que foi removido
              this.getPedidos();

            },

            //Requisição de atualização dos status do pedido
            async updateBurger(event, id){
              const option = event.target.value;

              const dataJson = JSON.stringify({ status: option })

              const req = await fetch(`http://localhost:3000/burgers/${id}`,{
                method: "PATCH",// O patch e um tipo de update que serve para atualizar uma coisa especifica no caso aqui é o status
                headres: {"Content-Type": "application/json"},
                body: dataJson
              });

              const res = await req.json()

              this.msg = `O pedido Nº ${res.id} foi atualizado para o status de ${res.status}`

              setTimeout(() => {
                this.msg = ""
              }, 5000)

              console.log(res);
            }

        },
        mounted(){
            this.getPedidos()
            
        }
    }
</script>

<style scoped>
  #burger-table {
    max-width: 1200px;
    margin: 0 auto;
  }

  #burger-table-heading,
  #burger-table-rows,
  .burger-table-row {
    display: flex;
    flex-wrap: wrap;
  }

  #burger-table-heading {
    font-weight: bold;
    padding: 12px;
    border-bottom: 3px solid #333;
  }

  .burger-table-row {
    width: 100%;
    padding: 12px;
    border-bottom: 1px solid #CCC;
  }

  #burger-table-heading div,
  .burger-table-row div {
    width: 19%;
  }

  #burger-table-heading .order-id,
  .burger-table-row .order-number {
    width: 5%;
  }

  select {
    padding: 12px 6px;
    margin-right: 12px;
  }

  .delete-btn {
    background-color: #222;
    color:#fcba03;
    font-weight: bold;
    border: 2px solid #222;
    padding: 10px;
    font-size: 16px;
    margin: 0 auto;
    cursor: pointer;
    transition: .5s;
  }
  
  .delete-btn:hover {
    background-color: transparent;
    color: #222;
  }
  
</style>