<template>
  <div id="app">
    <div class="urna">
      <telaComponent
      :tela="tela"
      :numeroVoto="numeroVoto"
      :quantidadeNumeros="quantidadeNumeros"
      :candidato="candidato"
      />
      <tecladoComponent 
      :adicionarNumero="adicionarNumero"
      :corrigir="corrigir"
      :confirmar="confirmar"
      :votarEmBranco="votarEmBranco"
      />
    </div>
  </div>
</template>

<script>

import '@/css/global.css'; 
import tecladoComponent from '@/components/teclado-component.vue';
import telaComponent from './components/tela-component.vue';
import confirmAudio from '@/assets/audios/confirm.wav';
import keyAudio from '@/assets/audios/key.wav';



export default {
  name: 'App',
  components: {
    tecladoComponent, telaComponent
  },
  methods:{
    adicionarNumero(numero){
      this.executarSom(keyAudio);
      if(this.numeroVoto.length== this.quantidadeNumeros){
        return false;
      }
      this.numeroVoto += ''+numero;
      this.verificarCandidato();
    },
    verificarCandidato(){
      if(this.numeroVoto.length < this.quantidadeNumeros){
        return false;
      }
      if(this.candidatos[this.tela][this.numeroVoto]){
        this.candidato = this.candidatos[this.tela][this.numeroVoto];
        return true;
      }
      this.candidato = {
        nome: 'Voto nulo',
        partido: 'Voto nulo',
        imagem: ''
      }
    },
    corrigir(){
      this.executarSom(keyAudio);
      this.limpar();
    },
    limpar(){
      this.candidato = {};
      this.numeroVoto = '';
    },
    confirmar(){
      if(this.numeroVoto.length < this.quantidadeNumeros){
        return false;
      }
      return this.avancarTela();
    },
    avancarTela(){
      this.executarSom(confirmAudio);
      if(this.tela == 'prefeito'){
        this.tela = 'vereador';
        this.quantidadeNumeros = 5;
        return this.limpar();
      }
      this.tela = 'fim';

      let instancia = this;
      setTimeout(function(){
          instancia.tela = 'prefeito';
          instancia.quantidadeNumeros = 2;
          return instancia.limpar();

      }, 3000);
    },
    votarEmBranco(){
      if(this.tela == 'fim') return false;
      this.limpar();
      this.avancarTela();

    },
    executarSom(arquivoSom){
      if(arquivoSom){
        let audio = new Audio(arquivoSom);
        audio.play();
      }
    }
  },
  data(){
    return {
      tela: 'prefeito',
      numeroVoto: '',
      quantidadeNumeros: 2,
      candidato:{},
      candidatos:{
            "prefeito":{
              "01":{
                "nome": "Barack",
                "partido": "Obama",
                "imagem": "https://upload.wikimedia.org/wikipedia/commons/thumb/8/8d/President_Barack_Obama.jpg/245px-President_Barack_Obama.jpg"
              },
              "08":{
                "nome": "Oprah",
                "partido": "Winfrey",
                "imagem": "https://s2-vogue.glbimg.com/yjfW9MPA7ATccOG18eE8iMhCqcU=/0x0:620x930/924x0/smart/filters:strip_icc()/i.s3.glbimg.com/v1/AUTH_5dfbcf92c1a84b20a5da5024d398ff2f/internal_photos/bs/2022/E/y/JD2w4LSv634iY3Dts9fw/2022-01-28-gettyimages-176445627.jpg"
              }
            },
            "vereador":{
              "01234":{
                "nome": "Beyonce",
                "partido": "Knowles",
                "imagem": "https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcTcqTzFnLP1f0YLmKLQi8383ka8Pic6Ya5-1w&usqp=CAU"
              },
              "08001":{
                "nome": "Martin Luther ",
                "partido": "King",
                "imagem": "https://cdn.pensador.com/img/authors/ma/rt/martin-luther-king-l.jpg"
    }
  }
}
    }
  }
}
</script>

<style>
#app {
background-color: var(--background-color);
width:100%;
height:100%;
display:flex;
justify-content:center;
align-items:center;
}

.urna {
  width: 1000px;
  height: 500px;
  background-color: white;
  padding: 30px;
  border-radius: 5px;
  display: flex;
  justify-content: space-between;
}
</style>
