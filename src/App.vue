<script setup>
import Listen from './components/Listen.vue'
</script>

<template>
   <nav class="navbar nav navbar-expand-lg navbar-light" style="background-color: #010a43;">
    <h2 class="navbar-brand" style="color: #ffb6c1;"><strong>Emo-Pulse</strong> <img src="/src/assets/emo_logo.png" class="align-logo" width="30" height="20" /></h2>
    <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarNav" aria-controls="navbarNav" aria-expanded="false" aria-label="Toggle navigation">
        <span class="navbar-toggler-icon"></span>
    </button>
    <!-- <div class="collapse navbar-collapse" id="navbarNav">
        <ul class="navbar-nav">
            <li class="nav-item">
                <a class="nav-link" href="#" style="color: #ffb6c1;">Home</a>
            </li>
            <li class="nav-item">
                <a class="nav-link" href="#" style="color: #ffb6c1;">About</a>
            </li>
            <li class="nav-item">
                <a class="nav-link" href="#" style="color: #ffb6c1;">Contact</a>
            </li>
        </ul>
    </div> -->
  </nav>
  <div class="container">
    <br/><br/>
    <h1>Welcome to Emo-Pulse</h1>
    <p>We're excited to offer you a unique experience with our AI sentiment analysis for customers.</p>


      <br/><br/>

      

     

     

      


       <Listen/>


      
</div>
</template>

<style scoped>
.logo {
  height: 6em;
  padding: 1.5em;
  will-change: filter;
}
.logo:hover {
  filter: drop-shadow(0 0 2em #646cffaa);
}
.logo.vue:hover {
  filter: drop-shadow(0 0 2em #42b883aa);
}

.nav {
  position: fixed;
  top: 0px;
  left: 0px;
  width: 100%;
  font-size: 20px;
  font-family: Arial, Helvetica, sans-serif;
  color: grey;
  
}



</style>
