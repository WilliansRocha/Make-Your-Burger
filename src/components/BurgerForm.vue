<template>
    <Message :msg="msg" v-show="msg" />
    <div>
        <form id="burger-form" method="POST" @submit="createBurger">
            <div class="input-container">
                <label for="nome">Nome do Cliente</label>
                <input type="text" id="nome" name="nome" v-model="nome" placeholder="Digite o seu nome" autocomplete="off"> <!-- [v-model] fica referente a propriedade data que é criado no componente-->
            </div>
            <div class="input-container">
                <label for="pao">Escolha o pão: </label>
                <select name="pao" id="pao" v-model="pao">
                <option>Selecione o seu pão </option>
                <option v-for="pao in paes" :key="pao.id" :value="pao.tipo">{{pao.tipo}}</option> <!--cria um v-for para pegar todos os paes, a chave é o proprio id que já tem no backend o value é o tipo de pao, e é mostrado o tipo de pao ao cliente -->
            </select>   
            </div>
            <div class="input-container">
                <label for="carne">Escolha a carne do seu Burger:</label>
                <select name="carne" id="carne" v-model="carne">
                <option value="">Selecione o tipo de carne</option>
                <option v-for="carne in carnes" :key="carne.id" :value="carne.tipo">{{carne.tipo}}</option>
            </select>   
            </div>
            <div id="opcionais-container" class="input-container">
                <label id="opcionais-title" for="opcionais">Selecione os opcionais:</label>
                <div class="checkbox-container" v-for="opcional in opcionaisdata" :key="opcional.id"> <!--coloca o v-for no componente que quer repetir no caso a div com input e span juntos-->
                <input type="checkbox" name="opcionais" v-model="opcionais" :value="opcional.tipo" id="opc-lanche">
                <span>{{ opcional.tipo}}</span>
                </div>
            </div>
            <div class="input-container">
                <input type="submit" class="submit-btn" value="Criar meu Burger!">
            </div>
        </form>
    </div>
</template>
<script>
    import Message from './Message.vue';
    export default{
        name: "BurgerForm",
        data(){
        return{
            //Dados que vem do servidor
            paes: null,
            carnes: null,
            opcionaisdata: null,
            //Abaixo são os dados que são enviados
            nome: null,
            pao: null,
            carne:null,
            opcionais:[],
            status:"Solicitado",
            msg: null
            }
        },
        methods:{
            async getIngredientes(){
                const req = await fetch("http://localhost:3000/ingredientes"); // requisicao para o "backend" criado em json
                const data = await req.json(); // transforma esses dados em json e manda para data, que será a variavel utilizada para pegar os dados da requisicao
                this.paes = data.paes; // as propriedades passam a receber os dados que vieram da requisicao
                this.carnes = data.carnes;
                this.opcionaisdata = data.opcionais;
            },

            async createBurger(e){
             
                     // passa aum argumento de evento porque vai parar o evento do formulario quando clicar no submit
                e.preventDefault();
                const data = { // cria um objeto para salvar os dados do formulario
                    nome: this.nome,
                    carne: this.carne,
                    pao: this.pao,
                    opcionais: Array.from(this.opcionais),
                    //opcionais:Array.from(this.opcionais), //para criar corretamente o array de opcionais deve ser utilizado esse metodo array.from
                    status: "Solicitado"
                }
                const dataJson = JSON.stringify(data) // transformando o dado em um texto  Json válido
              
                // Essa parte abaixo faço a requisição, a api no burgers que esta vazio ala no arquivo db.json, passo também o tipo de metodo que é post, 
                const req = await fetch("http://localhost:3000/burgers",{
                method: "POST",
                headers: {"Content-Type": "application/json"}, // mostrar que estou comunicando em json, tipo de conteudo aplication json 
                body: dataJson // o corpo da requisicao vai enviar os dados de dataJson como texto
            });

            const res = await req.json();
            
            //colocar uma msg de sistema
            this.msg = `Pedido N° ${res.id} realizado com sucesso`
            //limpar mensagem
            setTimeout(()=> this.msg = "", 3000);
            //limpar os campos
            this.nome = "";
            this.carne = "";
            this.pao = "";
            this.opcionais = "";
            
        }
                
               
        },
        mounted(){
            this.getIngredientes();
        },
        components:{
            Message
        }
   
    }

</script>

<style scoped>
    #burger-form{
        max-width: 35%;
        margin: 0 auto;
    }
    .input-container{
        display: flex; /** A LABEL FICA EM UMA LINHA E O INPUT EM OUTRA */
        flex-direction: column;
        margin-bottom: 20px;
    }
    label{
        font-weight: bold;
        margin-bottom: 15px;
        color: #222;
        padding: 5px 10px;
        border-left: 4px solid #FCBA03;
    }
   
    input,select{
        padding: 5px 10px;
        /*width:30%; não precisa porque ja defini um porcentagem antes*/
    }

    #opcionais-container{
        flex-direction: row; /** para a label ficar embaixo*/
        flex-wrap: wrap;
    }
    #opcionais-title{
        width: 100%; /** faz com que os opcionais não fiquem na frente dessa label */
    }

    .checkbox-container{
        display: flex;
        align-items: flex-start;
        width: 50%;
        margin-bottom: 10px;
    }

    .checkbox-container span, .checkbox-container input{
        width: auto;
    }

    .checkbox-container span{
        margin-left: 6px;
        font-weight: bold;
    }

    .submit-btn{
        background-color: #222;
        color: #FCBA03;
        font-weight: bold;
        border: 2px solid #222;
        padding: 10px;
        font-size: 16px;
        margin: 0 auto;
        cursor: pointer;
        transition: .5s;
        border-radius: 5px;
    }

    .submit-btn:hover{
        background-color: transparent;
        color: #222;
    }
    #label-diferente{
        border: none;
        cursor: pointer;
        padding: none;
    }
</style>