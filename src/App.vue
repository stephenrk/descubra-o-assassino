<template>
  <div class="shade">
    <div class="blackboard">
      <div class="form">
        <p v-if="errors.length">
          <ul class="message">
            <li class="error" v-for="error in errors">{{ error }}</li>
          </ul>
        </p>
        <p v-if="success !== null && success !== ''">
          <ul class="message">
            <li class="success">{{ success }}</li>
          </ul>
        </p>
        <div class="form-group">
          <label for="txtname">Suspeito: </label>
          <select v-model="suspeitoSelecionado">
            <option value>Selecione...</option>
            <option v-for="suspeito in suspeitos" v-bind:value="suspeito.Id">
              {{ suspeito.Nome }}
            </option>
          </select>
        </div>
        <div class="form-group">
          <label>Local: </label>
          <select v-model="localSelecionado">
            <option value>Selecione...</option>
            <option v-for="local in locais" v-bind:value="local.Id">
              {{ local.Nome }}
            </option>
          </select>
        </div>
        <div class="form-group">
          <label>Arma: </label>
          <select v-model="armaSelecionada">
            <option value>Selecione...</option>
            <option v-for="arma in armas" v-bind:value="arma.Id">
              {{ arma.Nome }}
            </option>
          </select>
        </div>
        <button id="btnEnviar" v-show="btnVisivel" type="submit" @click="submit()">Enviar</button>
        <button id="btnRestart" v-show="!btnVisivel" type="button" @click="restart()">Jogar Novamente</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data () {
    return {
      misterioId: null,
      suspeitos: [],
      locais: [],
      armas: [],
      suspeitoSelecionado: '',
      localSelecionado: '',
      armaSelecionada: '',
      errors:[],
      success: null,
      btnVisivel: true
    }
  },
  created() {
    this.$http.get('https://handson.eniwine.com.br/api/descubraoassassino')
      .then(res => res.json(), err => console.log(err))
      .then(data => this.misterioId = JSON.parse(data).misterioId, err => console.log(err));

    this.$http.get('https://handson.eniwine.com.br/api/descubraoassassino/criminosos')
      .then(res => res.json(), err => console.log(err))
      .then(suspeitos => this.suspeitos = JSON.parse(suspeitos), err => console.log(err));
    
    this.$http.get('https://handson.eniwine.com.br/api/descubraoassassino/locais')
      .then(res => res.json(), err => console.log(err))
      .then(locais => this.locais = JSON.parse(locais), err => console.log(err));

    this.$http.get('https://handson.eniwine.com.br/api/descubraoassassino/armas')
      .then(res => res.json(), err => console.log(err))
      .then(armas => this.armas = JSON.parse(armas), err => console.log(err));
  },
  methods: {
    submit: function() {
      if(this.suspeitoSelecionado && this.localSelecionado && this.armaSelecionada) {
        this.$http.post('https://handson.eniwine.com.br/api/descubraoassassino',
          { IdSuspeito: this.suspeitoSelecionado, IdArma: this.armaSelecionada, IdLocal: this.localSelecionado, IdMisterio: this.misterioId }
        )
        .then(res => res.json())
        .then(function(res) {
          let codRetorno = JSON.parse(res);

          if (codRetorno === 0) {
            this.success = 'Parabéns! Você acertou!';
            this.btnVisivel = false;
          } else if (codRetorno === 1) {
            this.errors.push('Assassino incorreto');
          } else if (codRetorno === 2) {
            this.errors.push('Local incorreto');
          } else if (codRetorno === 3) {
            this.errors.push('Arma incorreta');
          }
        })
      }
      this.errors = [];
      this.success = ''
      if(!this.suspeitoSelecionado) this.errors.push("Selecione o suspeito");
      if(!this.localSelecionado) this.errors.push("Selecione o local");
      if(!this.armaSelecionada) this.errors.push("Selecione a arma");
    },
    restart: function() {
      location.reload()
    }
  }
}
</script>

<style>

body {
  height: 100%;
  background: url('./assets/concrete-wall-background.jpg') center center fixed;
  background-size: cover;
}

.shade {
  overflow: auto;
  position: absolute;
  top: 0;
  left: 0;
  bottom: 0;
  right: 0;
  background-image: linear-gradient( 150deg, rgba(0, 0, 0, 0.65), transparent);
}

.blackboard {
  position: relative;
  margin: 7% 15%;
  border: tan solid 12px;
  border-top: #bda27e solid 12px;
  border-left: #b19876 solid 12px;
  border-bottom: #c9ad86 solid 12px;
  box-shadow: 0px 0px 6px 5px rgba(58, 18, 13, 0), 0px 0px 0px 2px #c2a782, 0px 0px 0px 4px #a58e6f, 3px 4px 8px 5px rgba(0, 0, 0, 0.5);
  background-image: radial-gradient( circle at left 30%, rgba(34, 34, 34, 0.3), rgba(34, 34, 34, 0.3) 80px, rgba(34, 34, 34, 0.5) 100px, rgba(51, 51, 51, 0.5) 160px, rgba(51, 51, 51, 0.5)), linear-gradient( 215deg, transparent, transparent 100px, #222 260px, #222 320px, transparent), radial-gradient( circle at right, #111, rgba(51, 51, 51, 1));
  background-color: #333;
  z-index: 0;
}

@media(max-width: 950px) {
  .blackboard {
    margin: 7% auto;
  }
}

.blackboard:before {
  box-sizing: border-box;
  display: block;
  position: absolute;
  width: 100%;
  height: 100%;
  background-image: linear-gradient( 175deg, transparent, transparent 40px, rgba(120, 120, 120, 0.1) 100px, rgba(120, 120, 120, 0.1) 110px, transparent 220px, transparent), linear-gradient( 200deg, transparent 80%, rgba(50, 50, 50, 0.3)), radial-gradient( ellipse at right bottom, transparent, transparent 200px, rgba(80, 80, 80, 0.1) 260px, rgba(80, 80, 80, 0.1) 320px, transparent 400px, transparent);
  border: #2c2c2c solid 2px;
  content: "Descubra o assassino";
  font-family: 'Permanent Marker', cursive;
  font-size: 2.2em;
  color: rgba(238, 238, 238, 0.7);
  text-align: center;
  padding-top: 20px;
  z-index: -1;
}

.form {
  padding: 70px 20px 20px;
  z-index: 1
}

label {
  vertical-align: middle;
  font-family: 'Permanent Marker', cursive;
  font-size: 1.6em;
  color: rgba(238, 238, 238, 0.7);
}

p:nth-of-type(5) > label {
  vertical-align: top;
}

select:focus {
  background: rgba(238, 238, 238, 0.2);
  color: rgb(210, 180, 140);
}

select, button {
  vertical-align: middle;
  padding-left: 10px;
  background: none;
  border: none;
  font-family: 'Permanent Marker', cursive;
  font-size: 1.6em;
  color: #bda27e;
  line-height: .6em;
  outline: none;
}

select option {
  font-size: 0.8em;
}

button {
  cursor: pointer;
  color: rgba(238, 238, 238, 0.7);
  line-height: 1em;
  padding: 7px;
  margin-top: 20px;
  border-style: double;
}

button:hover, button:focus {
  background: rgba(238, 238, 238, 0.2);
  color: rgb(210, 180, 140);
}

::-moz-selection {
  background: rgba(238, 238, 238, 0.2);
  color: rgba(238, 238, 238, 0.2);
  text-shadow: none;
}

::selection {
  background: rgba(238, 238, 238, 0.4);
  color: rgba(238, 238, 238, 0.3);
  text-shadow: none;
}

.message {
  font-family: 'Permanent Marker', cursive;
}

.error {
  font-size: 0.8em;
  color: red;
}

.success {
  font-size: 1.3em;
  color: green;
}

.form-group select {
  width: 100%;
}
</style>
