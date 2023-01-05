<template>
    <Message :msg="msg" v-show="msg" />
    <div id="burger-table">
        <div>
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
                <div class="order-number">{{burger.id}}</div>
                <div>{{ burger.nome }}</div>
                <div>{{burger.pao}}</div>
                <div>{{burger.carne}}</div>
                <div>
                    <ul>
                        <li v-for="(opcional, index) in burger.opcionais" :key="index">{{ opcional }}</li>
                        
                    </ul>
                </div>
                <div>
                    <select name="status" class="status" @change="updateBurger($event, burger.id)">
                        <option v-for="s in statusData" :key="s.id" :value="s.tipo" :selected="burger.status == s.tipo">{{ s.tipo }}</option> <!--o selected seleciona o status do pedido e deixa marcado -->
                        
                    </select>
                    <button class="delete-btn" @click="deleteBurger(burger.id)">Cancelar</button>
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
        return{
           burgers: null,
           burger_id: null,
           statusData:[],
           msg: null
        }
    },
    components:{
        Message
    },
    methods:{
        async getPedidos(){
            const req = await fetch("http://localhost:3000/burgers");
            const data = await req.json();   

            this.burgers = data;

            console.log(this.burgers);

            // resgatar os status
        },

        async getStatus(){
            const req = await fetch("http://localhost:3000/status");
            const data = await req.json();
            this.statusData = data;
        },

        async deleteBurger(id){
            const req = await fetch(`http://localhost:3000/burgers/${id}`,{
                method: "DELETE"
            }); // metodo para apagar os dados do pedido diretamente da api

            const res = await req.json();

            //msg de deletado
            this.msg = `Pedido excluído com sucesso`
            //limpar mensagem
            setTimeout(()=> this.msg = "", 3000);

            this.getPedidos();
        },

        async updateBurger(event, id){ // passa o evento e id como parametro
            const option = event.target.value; // cria no select o evento @change e quando mudar ele manda o valor para essa variavel

            const dataJson = JSON.stringify({ status : option }); // status passa a ser o valor adquirido do evento de mudanca, e e salvo no dataJson
            const req = await fetch(`http://localhost:3000/burgers/${id}`,{ // atraves do id passado por parametro é procurado na api e modificado com o valor dataJson
            method:"PATCH", // esse metodo modifica apenas a propriedade passada
            headers: {"Content-Type" : "application/json"},
            body: dataJson
        });
            const res = await req.json();
            this.msg = `Pedido N° ${res.id} foi atualizado para | ${res.status} |`
            //limpar mensagem
            setTimeout(()=> this.msg = "", 3000);
            
        }
        
    },
    mounted(){
            this.getPedidos();
            this.getStatus();
        },
}
</script>


<style scoped>
*{
    box-sizing: border-box;
}
    #burger-table{
       max-width: 90%;
       margin: 0 auto;
    }

    #burger-table-heading, #burger-table-rows, .burger-table-row{
        display: flex;
        flex-wrap: wrap;
    }
    #burger-table-heading{
        font-weight: bold;
        padding: 12px;
        border-bottom: 3px solid #333;
    }

    #burger-table-heading div, .burger-table-row div{
       width: 19%; 
    }
    .burger-table-row{
        width: 100%;
        padding: 12px;
        border-bottom:1px solid #ccc;
    }

    #burger-table-heading .order-id, .burger-table-row .order-number{
        width: 5%;
    }

    select{
        padding: 12px 6px; 
    }

    .delete-btn{
        background-color: #222;
        color: #FCBA03;
        font-weight: bold;
        border: 2px solid #222;
        padding: 10px;
        margin: 0 auto;
        cursor: pointer;
        transition: .5s;
        border-radius: 5px;
    }

    .delete-btn:hover{
        background-color: transparent;
        color: #222;
    }

    
</style>