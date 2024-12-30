<template>
    <div>
        <Message :msg="msg" v-show="msg" />
        <form id="burger-form" @submit="createBurger">
            <div class="input-container">
                <label for="nome">Nome do Cliente:</label>
                <input type="text" id="nome" name="name" v-model="nome" placeholder="Digite o seu nome" />
            </div>
            <div class="input-container">
                <label for="pao">Escolha o pão:</label>
                <select name="pao" id="pao" v-model="pao">
                    <option value="">Selecione o seu pão</option>
                    <option v-for="pao in paes" :key="pao.id" :value="pao.tipo">
                        {{ pao.tipo }}
                    </option>
                </select>
            </div>

            <div class="input-container">
                <label for="carne">Escolha a carne do seu Burguer:</label>
                <select name="carne" id="carne" v-model="carne">
                    <option value="">Selecione o tipo de carne</option>
                    <option v-for="carne in carnes" :key="carne.id" :value="carne.tipo">
                        {{ carne.tipo }}
                    </option>
                </select>
            </div>

            <div id="opcionais-container" class="input-container">
                <label id="opcionais-title" for="opcionais">Selecione os opcionais:</label>
                <div class="checkbox-container" v-for="opcional in opcionaisdata" :key="opcional.id">
                    <input type="checkbox" name="opcionais" v-model="opcionais" :value="opcional.tipo" />
                    <span>{{ opcional.tipo }}</span>
                </div>
            </div>

            <div class="input-container">
                <input type="submit" class="submit-btn" value="Criar meu Burger!" />
            </div>
        </form>
    </div>
</template>

<script>
import Message from './Message.vue';

export default {
    name: "BurguerForm",
    data() {
        return {
            paes: null,
            carnes: null,
            opcionaisdata: null,
            nome: "",
            pao: "",
            carne: "",
            opcionais: [],
            status: "Solicitado",
            msg: null,
        };
    },
    methods: {
        async getIngredientes() {
            try {
                const req = await fetch("http://localhost:3000/ingredientes");
                const data = await req.json();
                this.paes = data.paes;
                this.carnes = data.carnes;
                this.opcionaisdata = data.opcionais;
            } catch (error) {
                console.error('Error fetching ingredientes:', error);
            }
        },

        async createBurger(e) {
            e.preventDefault();

            // Validação: Verificar se todos os campos obrigatórios foram preenchidos
            if (!this.nome || !this.pao || !this.carne) {
                this.msg = "Por favor, preencha todos os campos obrigatórios.";
                setTimeout(() => { this.msg = null }, 3000);
                return;
            }

            const data = {
                nome: this.nome,
                carne: this.carne,
                pao: this.pao,
                opcionais: Array.from(this.opcionais),
                status: "Solicitado"
            };

            const dataJson = JSON.stringify(data);

            try {
                const req = await fetch("http://localhost:3000/burgers", {
                    method: "POST",
                    headers: { "Content-Type": "application/json" },
                    body: dataJson,
                });

                const res = await req.json();
                console.log(res);  // Verifique a resposta completa

                // Corrigir a exibição do ID caso seja alfanumérico
                // Remover caracteres não numéricos (caso necessário)
                this.msg = `Pedido N° ${res.id.replace(/\D/g, '')} realizado com sucesso`; // Ajuste o ID para ser apenas numérico, se necessário

                // Limpar mensagem após 3 segundos
                setTimeout(() => { this.msg = null }, 3000);

                // Limpar campos do formulário
                this.nome = "";
                this.carne = "";
                this.pao = "";
                this.opcionais = [];
            } catch (error) {
                console.error("Erro ao criar o pedido:", error);
                this.msg = "Ocorreu um erro ao criar seu pedido. Tente novamente!";
                setTimeout(() => { this.msg = null }, 3000);
            }
        }
    },
    mounted() {
        this.getIngredientes();
    },
    components: {
        Message
    }
};
</script>

<style scoped>
#burger-form {
    max-width: 400px;
    margin: 0 auto;
    padding: 20px; /* Adicionando padding para garantir que os elementos não fiquem muito colados nas bordas */
}

.input-container {
    display: flex;
    flex-direction: column;
    margin-bottom: 20px;
}

label {
    font-weight: bold;
    margin-bottom: 15px;
    color: #222;
    padding: 5px 10px;
    border-left: 4px solid #FCBA03;
}

input, select {
    padding: 5px 10px;
    width: 100%;  /* Ajustado para garantir que os campos ocupem a largura total disponível */
}

#opcionais-container {
    display: flex;
    flex-wrap: wrap; /* Permitindo que os itens se ajustem em várias linhas */
    gap: 15px; /* Adicionando espaçamento entre os checkboxes */
}

.checkbox-container {
    display: flex;
    align-items: center;
    width: 45%; /* Ajustando a largura para se adaptar melhor aos contêineres */
    margin-bottom: 20px;
}

.checkbox-container input {
    width: auto;
    margin-right: 10px; /* Espaçamento entre o checkbox e o texto */
}

.checkbox-container span {
    font-weight: bold;
}

.submit-btn {
    background-color: #222;
    color: #FCBA03;
    font-weight: bold;
    border: 2px solid #222;
    padding: 10px;
    font-size: 16px;
    cursor: pointer;
    transition: 0.3s;
    width: 100%; /* Garantindo que o botão ocupe toda a largura disponível */
    text-align: center; /* Centralizando o texto dentro do botão */
}

.submit-btn:hover {
    background-color: transparent;
    color: #222;
}

#opcionais-title {
    width: 100%;
    font-weight: bold;
    margin-bottom: 10px;
}
</style>
