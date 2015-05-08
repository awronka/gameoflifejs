// Ok so let me start my explanation. I'm going to try to go into as much depth as possible. Or at least as much as I can at this point. I'm not Batman level expert. I'm just Sider-Man, Gambit...

var gameOfLife = {
  width: 12,
  height: 12,
  stepInterval: null,
//Ok, so the three above items are the variables that make your table. Width 12, height 12, and step Interval null. 
  createAndShowBoard: function () {
  // this function is named what it does. createsAndshowstheboard. good stuff.
    var goltable = document.createElement("tbody");
    
    // build Table HTML. Duh. createElement is awesome because it places items right inside our HTML doc. If you go to the chrome console you'll find it right in there even though you didnt put it there yourself. As you can see below we set the tablehtml variable. This will be defined by making a double four loop which will create spearete id's for height and width. The four loop tells when the boxes should end. What this fails to mention here is tbody and td are elements in HTML which create tables. This was unclear to me at first since as I said, "I'm not Batman at html"
    var tablehtml = '';
    for (var h=0; h<this.height; h++) {
      tablehtml += "<tr id='row+" + h + "'>";
      for (var w=0; w<this.width; w++) {
        tablehtml += "<td data-status='dead' id='" + w + "-" + h + "'></td>";
      }
      tablehtml += "</tr>";
    }
    goltable.innerHTML = tablehtml;
    
    // add table to the #board element
    var board = document.getElementById('board');
    //this places the table inside the board element inside the html. check the console. It's there fools. 
    board.appendChild(goltable);
    
    // once html elements are added to the page, attach events to them. This makes it so buttons work. All buttons must be called inside the following function or else they wont work. 
    this.setupBoardEvents();
  },

  forEachCell: function (iteratorFunc) {
    var cell = document.getElementsByTagName('td') //get the elementbythe tag name 
    for(var i = 0; i< cell.length; i ++){
      iteratorFunc(cell[i])
      //this is your basic forEach function. It runs a function on an array of elements. In this case it gets all the elements from the document.getElementsByTagName function. Which is a method that allows for you to access a series of elements by the id. I then used a for loop to apply the function to each elemnt in the id. 
    }
  },
  
  setupBoardEvents: function() {
    // Ok here's where the hard part begins. I needed to code this stuff. Well, much of it. 
    
    var onCellClick = function (e) {
 
      if (this.getAttribute('data-status') == 'dead') {
        this.className = "alive";
        this.setAttribute('data-status', 'alive');
      } else {
        this.className = "dead";
        this.setAttribute('data-status', 'dead');
      }
    };// This was an example function. It toggles the data between an alive status and a dead status. This is how the 'game of life' works. 

    //Step number one: make onCellClick apply to all the pieces of boxes. This part was easy. Kind of. I wasn't sure how the this. part would work because part of the lecture. 

    //Question one: Why do all the buttons have to be inside SetupBoardEvents inorder to have functionality. 

    //Back to the forEach though. So forEach takes a function and I need to use the onclick method thats included in the javascript library. It knows what to do.    
    this.forEachCell( function(cell){
      cell.onclick = onCellClick;
    });
  this.enableAutoPlay();
  document.getElementById('step_btn').onclick = this.step.bind(this);
  document.getElementById('clear_btn').onclick = this.clear.bind(this);
  
  document.getElementById('reset_btn').onclick = this.resetRandom.bind(this);
//Above is where I had to use Bind. This should have been told straight forward as you end up playing around with things that dont work. Bind needs to be used with the buttons. In this case we want to bind this to the 'this'methods that we wrote for each putton. Why you ask? Because when you click on anything on the screen it gets the identity of this, or at least I think it does. See told you... not Batman at this yet. 
  },

  step: function () {
    var toToggle = [];
  this.forEachCell(function (cell) {
    var numLiveNeighbors = countLiveNeighbors(cell);
    // So right here you're like, "Where the hell did this count live Neighbors" thing come from. It's a good question. Go to the function below titled countLiveNeighbors. 

    //So If you look at the step function now you got the number of live neighbors. It then checks
    if (status(cell) == 'dead') {
      if (numLiveNeighbors === 3) {
        toToggle.push(cell);//push it in the toToggle array
      }
    } else {
      if (numLiveNeighbors > 3 || numLiveNeighbors < 2) {
        toToggle.push(cell);
      }
    }//If it has two neighbors it does not get pushed into the cell and gets left alone when we use the...
  });
  toToggle.forEach(function (cell) {//Toggle will use that handy dandy function that they provided for us above in the onCellclick function. This way it will change. We keep it separate to keep our code ledgible. 
    toggle(cell);
  });
    
  },  

  play: function(){//The play function 
  var selfStep = this.step.bind(this);
  self.stepInterval = setInterval(selfStep, 200);//Sets this to the window object. Self means window object. This is a windo timer it will do what the function says at a given interval
  this.playing = true;//This in this case is everything in the button.You don't need this but it looks nice. You could just use the first line and the boolean
  this.playButton.classList.remove('btn-primary');
  this.playButton.classList.add('btn-danger');
  this.playButton.textContent = 'Stop';//Changes the text to stop in the button to make it look cooler. If you look up what set interval does. It takes a function and in this case its the button from SetBoardEvents and has it continue on the window opbect.

},
pause:function () {
  clearInterval(self.stepInterval);//this will remove the timer
  this.playing = false; //set all the other information back to the original 
  this.playButton.classList.remove('btn-danger');
  this.playButton.classList.add('btn-primary');
  this.playButton.textContent = 'Play';
},

  enableAutoPlay: function () {
//Sets self to this will be what you click on. 
  var self = this;
  var playButton = document.getElementById('play_btn');
// in this case you set self which is this to the play btn. 
  self.playButton = playButton;
//Then onclick it runs the play or pause function depending on the state of playing. So simple now that I see it. Now I just need to rewrite this code like 50 times and im all good. BATMAN.
  playButton.onclick = function () {//This sets the function to selfin the context of the button you pushed. 
    if (!self.playing) {
      self.play();
    } else {
      self.pause();
    }
  };
    
  },
//The clear is the easiest one of all it sets all the boxes back to dead
  clear: function(){
    this.forEachCell(function(cell){
      status(cell, "dead");
    })
  },

  resetRandom :function () {
  this.pause();//this uses a tertiary operator to just randomly using math.random to put it at either dead or alive. 
  this.forEachCell(function (cell) {
    status(cell, Math.random() > 0.5 ? 'dead' : 'alive');
  });}


};
function status (cell, val) {
  if (val) {
    cell.setAttribute('data-status', val);
    cell.className = val;
  } else {
    return cell.getAttribute('data-status');
  }
}
//No, This is the first 
function getNeighbors (cell) {
  //Ok so here's our true first step in step! It creates an array and a variable to hold the id of the value. It forces the numbers to act as numbers even though they arn't and we use the map function to return new values. 
  var neighbors = [];
  var pos = cell.id.split('-').map(Number);
  for (var xOffset = -1; xOffset < 2; xOffset++) {
    //So if you look you can see that it starts at -1 and goes to two. Why? Because it checks for left and the right sides of the object. 
    for (var yOffset = -1; yOffset < 2; yOffset++) {// It also does that for the y off set and checks for the obove and below spaces in the the object. 
      var x = pos[0] + xOffset,
          y = pos[1] + yOffset;
       if (xOffset == 0 && yOffset == 0) {
         continue;// This keeps us from adding the live cell into the array. 
      }
      var neighbor = document.getElementById(x + '-' + y);
      if (neighbor) {//So then we push all the neighbors into an array using the getElementById method. This concludes our function and it should work at this point. So Lets go back to our countLiveNeighbors function
        neighbors.push(neighbor);
      }
    }
  }
  return neighbors;//oh always return
}
//Here's the first step in order to use the 'step' function. You need to count how many live neigbors there, but wait a minute. There's a getNeighbors function. Where'd that come from. It's above. Go look at it. 
function countLiveNeighbors (cell) {
  //So here we use the reduce function to check through to see how many of the neighbors have the 'dead' value. This allows us to 
  return getNeighbors(cell).reduce(function (sum, neighbor) {
    var total;
    if (status(neighbor) == 'dead') {
      total = 0;
    } else {
      total = 1;
    }
    return sum + total;
  }, 0);//The zero if you are asking is our initial value for the function. So now go back up to the real 'step' function
}

function toggle (cell) {//This is the same as the one in the Cell function 
  if (cell.getAttribute('data-status') == 'dead') {
    cell.className = "alive";
    cell.setAttribute('data-status', 'alive');
  } else {
    cell.className = "dead";
    cell.setAttribute('data-status', 'dead');
  }
}



  gameOfLife.createAndShowBoard();
