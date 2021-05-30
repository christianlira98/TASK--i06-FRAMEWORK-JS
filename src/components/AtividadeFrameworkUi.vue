<template>
  <div id="AtividadeFrameworkUi">
    <h1>Exemplo de Manipulação de Array e Objetos em Javascript</h1>

    <fieldset>
        <legend>Manutenção de Funcionários</legend>
        <p>Por favor, preencha os dados abaixo: </p>
        <div style="display: flex;">
            <label for="cpf">CPF:</label><br>
            <input id="cpf" class="margin-2" type="text" v-model="cpf" pattern="\d{3}\.\d{3}\.\d{3}-\d{2}"><br>

            <label for="nome">Nome:</label><br>
            <input id="nome" class="margin-2" type="text" v-model="nome"><br>

            <label for="cargo">Cargo:</label><br>
            <input id="cargo" class="margin-2" type="text" v-model="cargo"><br>
            
            <label for="salario">Salário:</label><br>
            <input id="salario" class="margin-2" type="number" v-model="salario"><br>

            <button id="btn" v-on:click="getFormData()">Adicionar</button>
        </div>
    </fieldset>


    <h2 class="outer-blue-border">Lista de Funcionários</h2>

    <div id="buttons" style="display: flex;">
        <button v-on:click="sort('cpf')">Ordernar por cpf</button>
        <button v-on:click="sort('nome')">Ordernar por Nome</button>
        <button v-on:click="sort('salario')">Ordernar por Salário (DESC)</button>
        <label class="padding-top-3" for="salario">Salário:</label><br>
        <input class="margin-2" type="number" v-model="salario_maior"><br>
        <button id="filterSal" v-on:click="filter_sal()" >Filtrar</button>
    </div>

    <table id="tableData" style="width:100%">
        <thead>
            <tr style="background-color: peachpuff; text-align: left;">
            <th>Cpf</th>
            <th>Nome</th>
            <th>Cargo</th>
            <th>Salário</th>
            <th></th>
            </tr>
        </thead>
        <tbody :key="componentKey">
            <tr v-for = "funcionario in funcionarios" :key="funcionario.id" v-show="funcionario.show">
                <td id="cpf">{{funcionario.cpf}}</td>
                <td id="nome">{{funcionario.nome}}</td>
                <td id="cargo">{{funcionario.cargo}}</td>
                <td id="salario">{{funcionario.salario}}</td>
                <td id="btn_remove">
                    <button v-on:click="remove(funcionario.cpf)">Remover</button>
                </td>
            </tr>
        </tbody>
      </table>
  </div>
</template>

<script>
export default {
  name: 'AtividadeFrameworkUi',
  data () {
      return {
        funcionarios: [],
        cpf: "",
        nome: "",
        cargo: "",
        salario:"",
        salario_maior:"",
        componentKey: 0
      }
  },
  methods: {
    getFormData: function () {
        const funcionario = {
            cpf: this.cpf,
            nome: this.nome,
            cargo: this.cargo,
            salario: this.salario,
            show: true
        }

        let valid = this.valida(funcionario);
        let result = this.exists(funcionario.cpf)

        if(valid.length > 0) {
            this.alerta(valid);
        }else if(!result) {
            this.funcionarios.push(funcionario);
            this.clearForm();
            this.alerta("Funcionário cadastrado com sucesso!");
        }else {
            this.alerta("Funcionário com o CPF informado, já foi cadastrado anteriormente!");
        }
    },
    clearForm: function () {
        this.cpf = "",
        this.nome = "",
        this.cargo = "",
        this.salario = ""
    },
    alerta: function (text) {
        alert(text)
    },
    exists: function(cpf) {
        let result = false;

        for (let i = 0; i < this.funcionarios.length; i++) {
            if (this.funcionarios[i]['cpf'] === cpf) {
                result = true;
                break;
            }
        }
        return result;
    },
    valida: function (funcionario) {
        let result = "";
        const cpf_str = "CPF não corresponde ao padrão xxx.xxx.xxx-xx";
        const nome_str = "Nome precisa ter no mínimo 4 letras";
        const cargo_str = "Cargo precisa ter no mínimo 4 letras";
        const salario_str = "Salário não pode ser zero";
        const cpf_pattern = /\d{3}\.\d{3}\.\d{3}-\d{2}/gi;

        result = funcionario.cpf.match(cpf_pattern) ? result : result + "\n" + cpf_str;
        result = funcionario.nome.length >= 4 ? result : result + "\n" + nome_str;
        result = funcionario.cargo.length >= 4 ? result : result + "\n" + cargo_str;
        result = funcionario.salario > 0 ? result : result + "\n" + salario_str;

        return result;
    },
    sort: function(type) {
        this.funcionarios.sort(function (i, z) {
            let children_i = i[type];
            let children_z = z[type];

            if(type === 'salario') {
                return parseInt(children_z) - parseInt(children_i);
            }else if (type === 'cpf') {
                children_i = children_i.replaceAll('.', '');
                children_i = children_i.replaceAll('-', '');
                children_z = children_z.replaceAll('.', '');
                children_z = children_z.replaceAll('-', '');

                return parseInt(children_i) - parseInt(children_z) ;

            }
            return children_i.localeCompare(children_z, 'pt');
        });
    },
    remove: function(cpf) {
        this.funcionarios = this.funcionarios.filter(element => {
            return element['cpf'] !== cpf
        })
        alerta("Funcionário removido com sucesso");

    },
    filter_sal: function () {

        if(this.salario_maior === "") {
            
            for (let i = 0; i < this.funcionarios.length; i++) {
                this.funcionarios[i].show = true;
            }
        }else {
            for (let i = 0; i < this.funcionarios.length; i++) {
                if(parseInt(this.funcionarios[i]['salario']) <= parseInt(this.salario_maior)) {
                    this.funcionarios[i].show = false;
                }else {
                    this.funcionarios[i].show = true;
                }
            }
        }
        this.componentKey += 1;

    }
  }
  
}
</script>


<style scoped>
.margin-2 {
    margin: 2px;
}

.padding-top-3 {
    padding-top: 3px;
}

.outer-blue-border {
    border: 1px solid cyan;
}

table, th, td {
    border: 1px solid black;
    border-collapse: collapse;
  } 
</style>
