<script>
  import { onMount } from "svelte";

  let grid;
  let gridLength = 50;
  let platePos = gridLength -1;
  let directions = {
    left : 'left',
    right : 'right',
    middle : 'middle'
  }
  let arr = [];
  let plate_array = [13,14, 15, 16,17];
  let ballPrevRowPosition;
  let ballPrevColPosition;
  let ballLastColPosition;
  let ballLastRowPosition;
  let ballSpeed = 50;

  for (let i = 1; i <= gridLength; i++) {
    arr.push(i);
  }

  function createPlate(arr) {
    for (let n of arr) {
      const el = document.querySelector(`[data-boxid="${platePos}-${n}"]`);
      el.classList.add("board-box");
    }
  }




  function removeClass(arr, isLeft) {
    for (let n of arr) {
      let currentIndex;
      currentIndex = isLeft ? n + 1 : n - 1;
      const el = document.querySelector(`[data-boxid="${platePos}-${currentIndex}"]`);
      el.classList.remove("board-box");
    }
  }



  function dropBall(row, col) {
    let ball = document.querySelector(`[data-boxid="${row}-${col}"]`);
    ball.classList.add("ball");
    let balldropInterval = setInterval(() => {
      let nextBall = document.querySelector(`[data-boxid="${row + 1}-${col}"]`);
      if (nextBall.classList.contains("board-box")) {
        clearInterval(balldropInterval);
        ballBounce(row, col);
        return;
      }

      nextBall.classList.add("ball");
      document
        .querySelector(`[data-boxid="${row}-${col}"]`)
        .classList.remove("ball");
      row++;
    }, ballSpeed);
  }


function wallBounceVertical(row,col){
  let nextBall;
  if(ballPrevRowPosition > row){
  let ballBounceInterval = setInterval(()=>{
  
    if(ballPrevColPosition > ballLastColPosition ){
      nextBall = document.querySelector(`[data-boxid="${row - 1}-${col-1}"]`);
// nextBall.classList.add("ball")
document.querySelector(`[data-boxid="${row}-${col}"]`)
        .classList.remove("ball");
        if(nextBall.classList.contains("wall-horizontal"))
        ballPrevColPosition = col;
        col--;
      
}
else{
  nextBall = document.querySelector(`[data-boxid="${row - 1}-${col+1}"]`);
// nextBall.classList.add("ball")
document.querySelector(`[data-boxid="${row}-${col}"]`)
        .classList.remove("ball");
        if(nextBall.classList.contains("wall-horizontal"))
        ballPrevColPosition = col;
        col++
   
      }

      nextBall.classList.add("ball")
row--;

if(nextBall.classList.contains("wall-horizontal"))
{
  ballPrevRowPosition =row+1;
  clearInterval(ballBounceInterval);    
  wallBounceHorizontal(row,col)

}

},ballSpeed)



    }
    else{

      let ballBounceInterval = setInterval(()=>{
    if(ballPrevColPosition > ballLastColPosition ){
      nextBall = document.querySelector(`[data-boxid="${row + 1}-${col-1}"]`);
// nextBall.classList.add("ball")
document.querySelector(`[data-boxid="${row}-${col}"]`)
        .classList.remove("ball");
        if(nextBall.classList.contains("wall-horizontal"))
        ballPrevColPosition = col;
        col--;
      
}
else{
  nextBall = document.querySelector(`[data-boxid="${row + 1}-${col+1}"]`);
// nextBall.classList.add("ball")
document.querySelector(`[data-boxid="${row}-${col}"]`)
        .classList.remove("ball");
        if(nextBall.classList.contains("wall-horizontal"))
        ballPrevColPosition = col;
        col++
   
}

nextBall.classList.add("ball")
row++;

if(nextBall.classList.contains("board-box"))
{
  ballBounce(row, col);
  clearInterval(ballBounceInterval)
}

},ballSpeed)

    }
}


function wallBounceHorizontal(row,col){
  let nextBall;
  
  let ballBounceInterval = setInterval(()=>{
    if(ballPrevColPosition <  ballLastColPosition ){
      nextBall = document.querySelector(`[data-boxid="${row + 1}-${col-1}"]`);
// nextBall.classList.add("ball")
document.querySelector(`[data-boxid="${row}-${col}"]`)
        .classList.remove("ball");
        ballPrevColPosition = col;
        col--;
}
else if(ballPrevColPosition === col){
  nextBall = document.querySelector(`[data-boxid="${row + 1}-${col}"]`);
// nextBall.classList.add("ball")
document.querySelector(`[data-boxid="${row}-${col}"]`)
        .classList.remove("ball");
        ballPrevColPosition = col;
        
}
else
{
  nextBall = document.querySelector(`[data-boxid="${row + 1}-${col+1}"]`);
// nextBall.classList.add("ball")
document.querySelector(`[data-boxid="${row}-${col}"]`)
        .classList.remove("ball");
        ballPrevColPosition = col;
        col++
}
nextBall.classList.add("ball")

row++;
if(nextBall.classList.contains("board-box"))
{
  ballBounce(row, col);
  clearInterval(ballBounceInterval)
}

if(nextBall.classList.contains("wall-vertical"))
{
  ballPrevRowPosition =row-1;
  clearInterval(ballBounceInterval);   
wallBounceVertical(row,col)

}


},ballSpeed)


}

 
 function bounceDirection(row,col,direction){
  let balldropInterval = setInterval(() => {
    let nextBall
    let currentCol = col;
    if(direction === directions.right)
      {
        currentCol++;
      }
    else if(direction === directions.left)
    {
      currentCol--;
    }  
    nextBall = document.querySelector(`[data-boxid="${row - 1}-${currentCol}"]`);
  
  
      if(direction === directions.middle)
      document.querySelector(`[data-boxid="${row}-${currentCol}"]`)
        .classList.remove("ball");

        else if(direction === directions.left){
          document.querySelector(`[data-boxid="${row}-${currentCol+1}"]`)
        .classList.remove("ball");
        }
        else if(direction === directions.right){
          document.querySelector(`[data-boxid="${row}-${currentCol-1}"]`)
        .classList.remove("ball");
        }

        if (nextBall.classList.contains("board-box")) {
        return;
      }

        if(nextBall.classList.contains("wall-vertical"))
    {ballPrevColPosition =currentCol;
      ballPrevRowPosition =row+1;
      ballLastColPosition = col;
      ballLastRowPosition = row;
      clearInterval(balldropInterval)
      wallBounceVertical(row,col);

    return;
    }

     if(nextBall.classList.contains('wall-horizontal')){

      ballPrevColPosition =currentCol;
      ballLastRowPosition = row +1;
      ballLastColPosition = col;
      ballLastRowPosition = row;

      clearInterval(balldropInterval)
     wallBounceHorizontal(row,col); 
    }

              nextBall.classList.add("ball");
      row--;
      col=currentCol;

    }, ballSpeed);
  }
 





  function ballBounce(row, col) {
 
    if(col=== plate_array[2]){
      bounceDirection(row,col,directions.middle)
    }
    else if(col=== plate_array[0]){
      bounceDirection(row,col,directions.left)
    }
    else if(col=== plate_array[3]){
      bounceDirection(row,col,directions.right)
    }
    else if(col=== plate_array[1]){
      bounceDirection(row,col,directions.left)
    }
    else if(col=== plate_array[4]){
      bounceDirection(row,col,directions.right)
    }
  }

  onMount(() => {
    createPlate(plate_array);

    const style = document.createElement("style");
    style.textContent = `
  .board-box{
    background: red;
  }

  .ball{
    background : green;
    border-radius: 50%;
  }
  .wall{
    background: brown;
  }

    `;
    document.head.appendChild(style);
    dropBall(15, 15);
  });

  function onKeyDown(e) {
    switch (e.keyCode) {
      case 37:
        console.log("left");
        if (plate_array[0] === 1) break;

        let left_arr = plate_array.map((li) => li - 1);
        console.log(left_arr);
        plate_array = left_arr;
        removeClass(plate_array, true);
        createPlate(plate_array);

        break;

      case 39:
        console.log("right");
        if (plate_array[4] === gridLength) break;
        let right_arr = plate_array.map((li) => li + 1);
        console.log(right_arr);
        plate_array = right_arr;
        removeClass(plate_array, false);
        createPlate(plate_array);
        break;
    }
  }
</script>

<section class="grid" bind:this={grid}>
  {#each arr as i}
    <div>
      {#each arr as j}
      {#if  i===1 || i=== gridLength|| j=== gridLength}
      <div data-row={j} data-column={i} data-boxid="{j}-{i}" class="box wall wall-vertical" />
      {:else if  j===1  }

      <div data-row={j} data-column={i} data-boxid="{j}-{i}" class="box wall wall-horizontal" />
      {:else }
        
        <div data-row={j} data-column={i} data-boxid="{j}-{i}" class="box " />
      {/if}
        {/each}
    </div>
  {/each}
</section>

<svelte:window on:keydown|preventDefault={onKeyDown} />

<style>
  .grid {
    display: flex;
  }
  .box {
    border: 1px solid #333;
    padding: 6px;
    /* height: 20px; */
    /* width: 20px; */
    display: grid;
    place-items: center;
  }
</style>
