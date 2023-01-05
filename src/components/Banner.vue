<template>
    <div id="main-banner">
        <h1>Make Your Burger</h1>
    </div>
</template>


<script>
    export default{
        name: 'Banner'
    
    }

    
</script>


<style scoped>
*{
    box-sizing: border-box;
}
   #main-banner{
    background-image: url('./imagetest/burger.jpg');
    background-position: 100%;
    background-repeat: no-repeat;
    background-position: center;  
    background-size: cover;
    height: 800px;
    display: flex;
    width: 100%;
    
    align-items: center; /** Alinha os itens ao centro na vertical*/
    justify-content: flex-start; /** posicao que ficará na horizontal */
   }

   #main-banner h1{
    color: #FFF;
    text-align: center;
    background-color: #222;
    padding: 10px 20px;
    margin-left: 30px;
    border-radius: 10px;
    width: 35vw;
    
   }

 
</style>>


