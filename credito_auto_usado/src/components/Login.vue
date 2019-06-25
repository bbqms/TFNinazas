<template>
  <div id="app">
  <v-app id="inspire">
     <main>
       <v-container fill-height>
         <v-layout align-center justify-center>
           <v-card height="500px">
             <v-container fill-height>
               <v-layout align-center justify-center>
                 <v-card-text>
                   <v-flex xs12 sm12 md12>
                      <v-text-field v-model="user"  label="Usuario"></v-text-field>
                    </v-flex>
                   <v-flex xs12 sm12 md12>
                      <v-text-field v-model="user"  label="Usuario"></v-text-field>
                    </v-flex>
                    <v-flex xs12 sm12 md12>
                      <v-text-field v-model="password" type="password"  label="Contraseña"></v-text-field>
                    </v-flex>  
                    <v-flex xs12>             
                      <v-btn color="blue darken-1"  @click.native="Logear">Entrar</v-btn>
                    </v-flex>                   
                 </v-card-text>
               </v-layout>
             </v-container>
           </v-card>
         </v-layout>
       </v-container>
     </main>
  </v-app>
</div>
</template>

<style>
.centered-input input {
  text-aling: center
}
</style>






<script>
export default {


}
</script>