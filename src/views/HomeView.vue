<template>
<div class="container-fluid">
  <div class="row">
    <div class="col-12 animate__animated animate__zoomIn">
      <div class="alert alert-info">
        Adivina número
        <ol>
          <li>Tú eliges el número de intentos</li>
          <li>Al inicio se generará un número aleatorio entre 1 y 100</li>
          <li>Conforme avances de nivel la complejidad aumentará</li>
        </ol>
      </div>
    </div>
    <div class="col-12 animate__animated animate__zoomInDown">
      <div class="card border-success">
        <div class="card-header bg-success border-success">
          <label v-if="nivel === 0"></label>
          <label class="text-white" v-else>
            Nivel: <b>{{ nivel }}</b>   Puntos: <b>{{ puntos }}</b>
          </label>
        </div>
        <div class="card-body">
          <div class="row">
            <div class="col-12 col-md-6 col-lg-3 mb-3" v-if="nivel === 0">
              <div class="input-group">
                <span class="input-group-text"><i class="fa-solid fa-question"></i></span>
                <input type="number" id="intentos" class="form-control" v-model="intentos"
                placeholder="Número de intentos" min="1" max="10"
                v-on:keyup.enter="iniciar()" autofocus>
              </div>
            </div>
            <div class="col-12 col-md-6 col-lg-3 mb-3" v-if="nivel === 0">
              <div class="d-grid col-12 mx-auto">
                <button class="btn btn-info animate__animated animate__bounceInLeft" @click="iniciar()">
                  <i class="fa-solid fa-circle-check"></i> Iniciar!
                </button>
              </div>
            </div>
            <div class="col-12 col-md-6 col-lg-3 mb-3" v-else>
              <label class="h2">Intentos restantes:</label>
              <label class="text-danger h2"> {{ intentos }}</label>
            </div>
            <div class="col-12 col-md-6 col-lg-3 mb-3">
              <div class="input-group">
                <span class="input-group-text"><i class="fa-solid fa-hashtag"></i></span>
                <input type="number" id="numero" class="form-control" v-model="numero"
                :disabled="nivel === 0" v-on:keyup.enter="jugar()"
                :placeholder="'Número entre '+min+' y '+max">
              </div>
            </div>
            <div class="col-12 col-md-6 col-lg-3 mb-3">
              <div class="d-grid col-12 mx-auto">
                <button class="btn btn-dark" :disabled="nivel === 0" @click="jugar()">
                  <i class="fa-solid fa-wand-magic-sparkles"></i> Adivinar
                </button>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</div>
</template>
<script>
  import Swal from 'sweetalert2';
  export default{
    data(){
      return{
        numero:null, min:1,max:100, nivel:0,aleatorio:0,pista:'',tope:0,puntos:0, intentos:null
      }
    },
    methods:{
      generarNumero(){
        var num = Math.floor(Math.random() * (Math.floor(this.max) -
        Math.ceil(this.min) + 1 )+ this.min);
        return num;
      },
      reiniciar(){
        window.setTimeout(function(){
          window.location.reload();
        },7000);
      },
      show_alerta(msj,txt,icono,foco=''){
        if(foco !== ''){
          document.getElementById(foco).focus();
        }
        Swal.fire({
          title:msj,text:txt,icon:icono,
          customClass:{confirmButton:'btn btn-primary', popup:'animated <oomIn'},
          buttonsStyling:false
        });
      },
      iniciar(){
        if(this.intentos === null || this.intentos < 1 || this.intentos > 10){
          this.show_alerta('Escribe un número del 1 al 10','','warning','intentos');
          this.intentos = null;
        }
        else{
          this.tope = this.intentos;
          this.aleatorio = this.generarNumero();
          console.log(this.aleatorio);
          this.nivel++;
          window.setTimeout(function(){
            document.getElementById('numero').focus();
          },500);
        }

      },
      jugar(){
        if(this.numero === null){
          this.show_alerta('Escribe un número','del '+this.min+' al '+this.max,'warning','numero');
        }
        else if(this.numero === this.aleatorio){
          this.nivel++;
          this.show_alerta('Felicidades!!! Has adivinado, el número fue '+
          this.aleatorio,'Pasarás al nivel '+this.nivel,'success','numero');
          this.max = this.max + 10;
          this.puntos = ((this.tope - this.intentos) === 0)
          ? this.puntos + 100 : this.puntos + ((this.intentos - this.tope + this.tope) * 10);
          this.intentos = this.tope;
          this.aleatorio = this.generarNumero();
          console.log(this.aleatorio);
        }
        else{
          if(this.intentos === 1){
            this.show_alerta('Suerte para la próxima, el número fue: '+
            this.aleatorio,'Tu puntaje fue de '+this.puntos,'error');
            this.nivel = 0;
            this.reiniciar();//Si no quieres reiniciar  comenta esta línea
            // y descomenta la siguiente:
            //this.puntos = 0;
          }
          else{
            this.pista = (this.numero > this.aleatorio) ? 'mayor':'menor';
            this.show_alerta('No has adivinado, aún puedes lograrlo',
            'Pista: el número '+this.numero+' es '+this.pista+' al número aleatorio',
            'error','numero');
          }
          this.intentos --;
        }
        this.numero = null;
      }
    }
  }
</script>