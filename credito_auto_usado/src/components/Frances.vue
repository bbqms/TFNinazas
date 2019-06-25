<template>
  <div>
  <v-form v-model="valid" >
    <v-container >
      <v-layout>
        <v-flex xs12 md4>
          <v-combobox v-model="Moneda"  :items="Monedas" label="Selecione su método de pago"></v-combobox>
        </v-flex>
        <v-flex xs12 md4>
          <v-text-field v-model="Ingresos" :type="number"  label="Ingresos" required></v-text-field>
        </v-flex>
        <v-flex xs12 md4>
          <v-text-field v-model="CuotaInicial" :type="number"  label="Cuota Inicial" required></v-text-field>
        </v-flex>
        <v-flex xs12 md4>
          <v-text-field v-model="PrecioVehiculo" :type="number" label="Precio del Vehiculo" required></v-text-field>
        </v-flex>
        <v-flex xs12 md4>
          <v-combobox :items="Plazos_de_Pago"  v-model="cuotas"  label="Plazos de Pago" return-object="false"></v-combobox>
        </v-flex>
      </v-layout>
         <div>
        <v-btn color="#1976D2" block @click="hallarflujos">Simular</v-btn>
        </div>
    </v-container>
  </v-form>

  <v-data-table :headers="headers" :items="flujos" class="elevation-1" :rows-per-page-items="[-1,50,25,10]" >
    <template v-slot:items="props">
      <td class="text-xs-center" >{{ props.item.numero }}</td>
      <td class="text-xs-center">{{ props.item.fecha }}</td>
      <td class="text-xs-center">{{ props.item.saldo }}</td>
      <td class="text-xs-center">{{ props.item.interes }}</td>
      <td class="text-xs-center">{{ props.item.segurodes }}</td>  
      <td class="text-xs-center">{{ props.item.segurobien }}</td>  
      <td class="text-xs-center">{{ props.item.amortizacion }}</td>  
      <td class="text-xs-center">{{ props.item.efec }}</td>  
      <td class="text-xs-center">{{ props.item.cuota }}</td>  
      
    </template>
  </v-data-table>
  </div>
</template>


<script>
  export default {
    data () {
      return {
        headers: [
          {
            text: '#',
            align: 'center',
            sortable: false,
            value: '#'
          },
          { text: 'Fecha',align: 'center',sortable: false, value: 'fecha' },
          { text: 'Saldo',align: 'center',sortable: false, value: 'saldo' },
          { text: 'Interes',align: 'center',sortable: false, value: 'interes' },
          { text: 'Seguro Desgravamen',align: 'center',sortable: false, value: 'seguo desgravamen' },
          { text: 'Seguro Bien',align: 'center',sortable: false, value: 'seguro bien' },
          { text: 'Amortizacion',align: 'center',sortable: false, value: 'amortizacion' },
          { text: 'Envio Fisico de Estado de Cuenta',align: 'center',sortable: false, value: 'EFEC' },
          { text: 'Cuota',align: 'center',sortable: false, value: 'cuota' }
        ],

        flujos: [
        
        ],


          flujoBase :{
            numero: null,
            fecha: null,
            saldo: null,
            interes: null,
            segurodes: null,
            segurobien: null,
            amortizacion: null,
            efec: null,
            cuota : null,
          },

        Monedas : ["Soles","Dolares"],
        
        Plazos_de_Pago : [
          {text:"12 Meses", value:12},
          {text:"24 Meses", value:24},
          {text:"36 Meses", value:36},
          {text:"48 Meses", value:48},
          {text:"60 Meses", value:60}
          ],





        //Datos Ingreso
        Moneda: "Soles",
        TEA: .1349,
        TNA: ((Math.pow(1+.1349,1/12)-1)*12*365/360).toFixed(6),
        Ingresos: null,
        CuotaInicial : null,
        PrecioVehiculo: null,
        TasaSeguroDesgravamen : 0.00050,
        TasaSeguroDesgravamenAnual : 0.00050 * 12,
        TasaSeguroVehicular: 0.0486,
        InteresSobreFinanciamientoCuotas: null,
        EFEC : 10,
        Fecha: new Date(),
        FechaInicial : new Date(),
        cuotas : null,
        n: 0,

      }
    },



    methods: {
      hallarflujos(){
        for ( var i = 0 ; i <= this.cuotas.value; i++) 
        {  
            this.crearflujo();
        
        }
      },
        crearflujo(){    
            //Interes Sobre financiamiento de Cuotas
            var ISFC = this.PrecioVehiculo * 0.40;
            this.InteresSobreFinanciamientoCuotas = ISFC;
            //Interes Sobre financiamiento de Cuotas


            let me = this;

            //fecha variable
            let fechaAux = me.Fecha;
            //fecha variable



            //dias del mes
            let dias = new Date(fechaAux.getFullYear(),fechaAux.getMonth(),0).getDate()
            //dias del mes



            //Tasa ajustada al plazo
            let i = ((me.TNA * dias)/365).toFixed(8);
            //Tasa ajustada al plazo


            //Tasa ajustada al plazo del 1er mes
            let iaux =  (me.TNA * parseInt(new Date(this.FechaInicial.getFullYear(),this.FechaInicial.getMonth(),0).getDate())/365).toFixed(8);
            let I = parseFloat(iaux);
            //Tasa ajustada al plazodel 1er mes

            
            //Crear y Objeto flujo
            let flujo = new Object; 
            flujo = {
            numero: me.n,
            fecha: fechaAux.getDate()+'-'+(fechaAux.getMonth()+1)+'-'+fechaAux.getFullYear(),
            saldo: me.PrecioVehiculo - me.CuotaInicial,
            interes: 0,
            segurodes: 0,
            segurobien: 0,
            amortizacion: 0,
            efec: 0,
            cuota : 0,
          };


         if(flujo.numero > 0)
         { 
         
          

          //calculando intereses
          flujo.interes = (this.flujoBase.saldo * i).toFixed(2);
          //calculando intereses


          //TDA ajustada al perdiodo : d
          let d = (this.TasaSeguroDesgravamenAnual * diasAux / 365 ).toFixed(13);

          flujo.segurodes = (this.flujoBase.saldo * d).toFixed(2);

          //TVA ajustada al periodo : m
          let m = (this.TasaSeguroVehicular * diasAux / 365).toFixed(11);

          flujo.segurobien = (this.PrecioVehiculo * m).toFixed(2);

          //Calcular Amortizacion
      
          let interesFC = ISFC * I;

          let cuotaFC = (43200) * (I /(1-Math.pow( 1 + I,-1 * this.cuotas.value) ));

          flujo.amortizacion = ( cuotaFC - interesFC - flujo.segurodes -flujo.segurobien - flujo.efec).toFixed(2);
  
          flujo.saldo = (this.flujoBase.saldo - flujo.amortizacion).toFixed(2);

          this.flujoBase.numero = flujo.numero;
          this.flujoBase.fecha = flujo.fecha; 
          this.flujoBase.saldo = flujo.saldo;
          this.flujoBase.interes = flujo.interes; 
          this.flujoBase.segurodes = flujo.segurodes; 
          this.flujoBase.segurobien = flujo.segurobien;
          this.flujoBase.amortizacion = flujo.amortizacion; 
          this.flujoBase.efec = flujo.efec;    
          this.flujoBase.cuota = flujo.cuota; 

         }
         else{

          this.flujoBase.numero = flujo.numero;
          this.flujoBase.fecha = flujo.fecha; 
          this.flujoBase.saldo = flujo.saldo;
          this.flujoBase.interes = flujo.interes; 
          this.flujoBase.segurodes = flujo.segurodes; 
          this.flujoBase.segurobien = flujo.segurobien;
          this.flujoBase.amortizacion = flujo.amortizacion; 
          this.flujoBase.efec = flujo.efec;    
          this.flujoBase.cuota = flujo.cuota; 
         }
          
          

          //hallar proxima fecha
          var fechaNueva = new Date(fechaAux.getTime() + (new Date(fechaAux.getFullYear(),1+fechaAux.getMonth(),0).getDate()*24*60*60*1000));
          this.Fecha=fechaNueva;

          

          flujo.cuota = (parseFloat(flujo.interes) + parseFloat(flujo.segurodes) + parseFloat(flujo.segurobien) + parseFloat(flujo.amortizacion) + parseFloat(flujo.efec)).toFixed(2);

          flujo.numero= me.n;

          me.flujos.push(flujo);

          
          this.n=this.n+1;

          
         

      },

    },      
  
  }
</script>