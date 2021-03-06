# #100DaysOfCode Log - Round 1 - Dashiell Bark-Huss

## Day 100
### 4/10/19

- ## Recipe Calculator
  Today is day 100. That's amazing. I feel really proud of myself for completing this challenge. Wait, I didn't complete it yet. So I got to calm down. Let's get to work!
  
  ## Should I Push This to github?
  
  I have been avoiding pushing this project to github because I need to set up dotenv, which I haven't done before. However, if you read my log and really want to see my work, let me know on twitter [@DashBarkHuss](https://twitter.com/DashBarkHuss) and I'll set it up. I didn't think it was a priority yet, but if it will help someone who is learning something from my log I'll push it. So let me know if you want me to push this. My original plan was to put off learning dotenv and just push this project when it's done.
  
  ## Still Too Many Event Listeners
  
  Last time we ended with this event listener. 
  
  ```javascript
  searchForm.addEventListener('submit', listener);
  ```
  
  And we removed the listener with `removeEventListener()` within the `listener` function. Remember, `listener` is the identifier for the returned function in `addFood()`, so the below returned function is `listener`:
  
  ```javascript
  function addFood(foodObjects){
        return function food(e){
            
            //.....code....
            
            e.target.removeEventListener('submit', listener); //_______________removed the event listener
        }
  }
  ```
  
  But we still have a problem. If the user does a search and then does another consecutive search, two of the listeners will be added without removing the first listener. The user needs to add a food item for `.removeEventListener()` to be reached. But they might not always want to add an item from a search.
  
  I had a feeling this was the wrong place to remove the event listener. I was right. I'm starting to see my gut is pointing me in the right direction, even when I'm not sure why.
  
  We need to remove the event listener in `searchResults()`. 
  
  ## Removing The Last Added Event Listener
  
  We can conditionally remove the lastest added event listener since we saved it in our `listener` identifier. If `listener` is defined, the code below removes the event listener.
  
  ```javascript
  var listener;
  function searchResults(results){
    const searchForm = document.querySelector("form[name=search_results]")

    let inputItems=[];
    results.forEach(x=>inputItems.push(`<input type="radio" name="food" value="${x.ndbno}">${x.name}</input><br>`));
    searchForm.innerHTML = inputItems.join("")+"<input type='submit' name='add' value='add'/>";
    const inputs = searchForm.querySelectorAll("inputs");

    const callback= addFood(results);
    if (listener) searchForm.removeEventListener('submit', listener);//____________conditionally remove event listener
    listener = callback;
    searchForm.addEventListener('submit', listener); 

  }
  ```
  ## Comparing The Nutrient Values
  
  I could make a new value for each object that represents the `proteinPerCalories`, `carbsPerCalories`, etc. Or I could make functions that get these values and not save them. 
  
  I'm going to start with using `sort()` to sort the nutrients by most protein per calorie.
  
  ## `sort()`
  
  I need to use the array method `sort()` to find the nutrient with the most protein, most fat, and most carbs per calories. I've used `sort()` before but I remember it being tricky. 
 
  Sort can take a compare function to specify how you want to sort the array.
  
  `array.sort(compareFunction)`
  
  The compare function should return -1, 0, or 1.
  
  
  >The function should return a negative, zero, or positive value, depending on the arguments, like:  
  >   function(a, b){return a-b}
  
  *from [w3schools, JavaScript Array sort() Method](https://www.w3schools.com/jsref/jsref_sort.asp)*
  
  ## Done
  
  Now the user can remove ingredients that they added. Once they are satisfied they can save the ingredients. This then gets a request for each nutrient report for the ingredient. The results are saved into an array, `nutrientRecipeArray`.
  
- ## Thoughts and Feelings:

  I'm super stoked! 100 Days completed!!!!!!!!!!!!!!!
  
  I learned so much.
  
  I learned a lot about learning. 
  
  I thought I would have learned many more topics by now. I actually learned a lot about one topic: vanilla javascript. There was more too learn than I expected. I feel really good about my programming capabilities compared to when I started. But I especially feel good that I know I can learn on my own if I remain consistent and patient. 
  
  I'm finally comfortable saying I'm a programmer!
  
  I feel like I'm officially a nerd. 
  
  Here's my victory dance.
  
  ![victory](log_imgs/victory_4-10.gif)
  
  

## Day 99
### 4/9/19

- ## Recipe Calculator

  ## Debugging Event Listeners
  
  ### Event Listeners Panel
  To see event listeners on a node, click the **Inspect Elements** button.
  
  ![inspect](https://developers.google.com/web/tools/chrome-devtools/inspect-styles/imgs/inspect-icon.png)
  
  Click the node you want to inspect. You should automatically be on the **Elements** tab now. In the **Event Listeners** panel, you'll see all the events on the node and it's ancestors if **Ancestors** is checked. You can find the handlers here.
  
  <img src="log_imgs/handler_4-9.PNG"  width="300"/> 
  
  Hover over the function to see the whole function.
  
  ### `getEventListeners()`
  
  You can also use [`getEventListeners(node)` in the cosole](https://developers.google.com/web/updates/2015/05/get-and-debug-event-listeners) to see the handler. Again, hover over the function after `listener:` to see the full callback.
  
  <img src="log_imgs/handler2_4-9.PNG"  width="350"/> 

  ### `monitorEvents()`
  
  >Once run, monitorEvents() will write out an event object and all of its associated data to the console when an event is triggered. You can narrow the output in a few ways, because it can become verbose quite quickly.
  
  *from [Monitoring Events in the JavaScript Console, oomphinc.com](https://www.oomphinc.com/notes/2014/01/monitoring-events-javascript-console/)*
  
  You can specify which node you want to monitor, for example `monitorEvents( $(“#myElement”)[0] )`. You can specify what events as well, `monitorEvents( $(“#myElement”)[0], “click” )` or `monitorEvents( $(“#myElement”)[0], [“click”, “blur”, “focus”] )`. These examples are using jQuery, that's the $ syntax.
 
  >You can unsubscribe from this event monitoring by using the command unmonitorEvents() while using the same parameters as the initial call.
  
  *from [Monitoring Events in the JavaScript Console, oomphinc.com](https://www.oomphinc.com/notes/2014/01/monitoring-events-javascript-console/)*
  
  ## Too Many Event Listeners
  
  I have multiple of the same event listeners on my `search_form` div. That's because I'm adding the event listener to the search form inside searchResults(), which get's called everytime the user does a search.
  
  This is causing issues. 
  
  A new event listener is added every time a search is made. The same handler is passed to `addEventListener()`, except for the parameters passed to it. The parameters passed each time are the results of the XHR, `results` (the food objects returned from each search).
  
  This is inside `searchForm()`:
  ```javascript
  searchForm.addEventListener('submit', addFood(results));
  ```
  
  We can pass params because `addFood()` is a closure. It returns a function. So actually the function that add closure ***returns*** is our callback. Unlike regular callbacks, `addFood(result)` actually gets called in that event listener function when the event listener is added, not later when the event is triggered. What get's triggered later? The function that `addFood(result)` returns.
  
  So every time `searchResults()` runs, we add another one of these event listeners with the new XHR response. But now, submit will trigger all of those callbacks and go through them all. Each one with the old XHR response. This creates issues.
  
  In our callback we try to find which object in the XHR response matches the selected input. The XHR response stored in the older callbacks(ex: a list of object results for a "beef" search) aren't going to match the selected input from the new search (ex: "Cauliflower, Raw" for a "cauliflower" search). This causes `undefined` to get passed to our ingredients, since nothing matched.
  
  We need to remove the old event listener before we add the new one.
  
  ## `removeEventListener()`
  
  Just like `addEventListener()` adds event listeners, `removeEventListener()` removes them.
  
  If we have an `addEventListener()` call inside the callback, `searchForm()`:
  
  ```javascript
  const searchForm = document.querySelector("form[name=search_results]")

  searchForm.addEventListener('submit', func); 
  ```
  
  We can later remove it like this from inside the `addFood(result)`, 'submit' event callback for the search form. We can reference the search form with `e.target` in this case.
  
  ```javascript
  e.target.removeEventListener('submit', func);
  ```
  
  Great. But how do we do this for the event we actually want to remove? Since it's a closure, it's more complicted. 
  
  ### This Doesn't Work:
  ```javascript
  e.target.removeEventListener('submit', addFood(foodObjects)) //foodObjects is the parameter where we pass results into, so this is the same as addFood(results)
  ```
  Probably because it's just creating another function that looks like our first one. It's not referencing the actual function.
  
  ### This Works:
  I found out from [this stackoverflow thread](https://stackoverflow.com/questions/19774202/how-to-removeeventlistener-that-was-added-using-closure) that we need to make a reference to that first function.
  
  1. Add a global variable.
  ```javascript
  var listener; //________________________________listener variable
  ```
  2. Set the variable. Use the global variable to add the event listener.
  ```javascript
  function searchResults(results){
        const searchForm = document.querySelector("form[name=search_results]")

        //.....code.....
    
        const callback= addFood(results);
        listener = callback; //___________________________set variable
        searchForm.addEventListener('submit', listener); //________________add
        
  }
  ```
  3. Remove the event listener.
  ```javascript
  function addFood(foodObjects){
        return function food(e){
            
            //.....code....
            
            e.target.removeEventListener('submit', listener); //_______________remove
        }
  }
  ```
  
- ## Thoughts and Feelings:

  I took a longer break today. I think this was smart. I felt stuck so I took a break. I ate a ketones Julian Bakery protein bar and a can of sardines, the two snacks I grabbed before I left my RV. I went for a short walk. Then my brain was refreshed.
  
  I'm at a cafe waiting for my RV's tires to be aligned. Since I have extra time now, I think I'll put my new [Wes Bos](https://wesbos.com/) dev stickers on! I accidentally got two packs in the mail. I'll be doing a giveaway for the other one to celebrate my 100 days tomorrow. Hopefully, I can also get my hands on a keto treat tomorrow to celebrate!
  
   <img src="log_imgs/stickers_4-9.jpg"  width="500"/>
   
   
## Day 98
### 4/8/19


- ## Recipe Calculator

  ## `forEach()` Returns Undefined
  
  I was trying to return values from `forEach()` but you can't.
  
  >...unlike map() or reduce() it always returns the value undefined and is not chainable
  
  [Mozilla Documentation](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach)
  
  ## Form Radio Input
  
  I used radio input for me form. They provide a way for the user to select one option from a list.
  
  More here:https://www.w3schools.com/tags/att_input_type_radio.asp

  [This stackoverflow thread](https://stackoverflow.com/questions/9618504/how-to-get-the-selected-radio-button-s-value) shows how to get the value for the slected item. Basically, you get which `input.checked == true` inside a loop. I used `forEach()`.
 
  ## Passing Event Objects to Closures?
   
  I want to try to use a closure but I also need to access the event object. I can't figure out how to do that.
   
  ```javascript
  function callback(a, b) {
   return function() {
     console.log('sum = ', (a+b));
   }
  }

  var x = 1, y = 2;
  document.getElementById('someelem').addEventListener('click', callback(x, y));
  ```
  
  Normally we'd put e here:
  
  ```javascript
  function callback(e) {//________________________e
   return function() {
     console.log('sum = ', (a+b));
   }
  }
  ```
  
  But we have params there already, `(a, b)`.
  
  ## Passing Event Objects to Closures!
  
  I found the answer here on [dev.to](https://dev.to/rpalo/closures-and-callbacks--30a9):
  
  ```javascript
  const openTab = tabName => {
    return e => {
      // Things in here have access to tabName *and* e
    }
  }
  ```
  
  Or in our code above:
  
  ```javascript
  function callback(a, b) {
   return function(e) { //_____________________________e goes here
     console.log('sum = ', (a+b));
   }
  }

  var x = 1, y = 2;
  document.getElementById('someelem').addEventListener('click', callback(x, y));
  ```
  
  ## Closure Using Old Values
  
  I ended with a bug where the closure is using old values from the last addEventListener callback. I think we need to remove the last event listener. I'm not sure. It still works but its going through unnecessary non-working callbacks probably from the old event listeners.
  
  ## Done:
  
  The app is now able to search and select recipe ingredients. Next, I need to get the nutrient info for the ingredients.
  
  ![screenshot](log_imgs/ndb_4-8.gif)
  
- ## Thoughts and Feelings:

  Lots of things to keep track of in this code. Felt a little distracted, hard to focus, but really only slightly. Felt motivated to code, just had trouble keeping track of everything I was doing in my code. Back hurting a little.
  

## Day 97
### 4/7/19

  This is the second time I noticed a bunch of my log was missing. What's happening? I added it back.
  
- ## Recipe Calculator

  ## `reduceRight()`
  
  I got `reduceRight()` to create the body of the function that creates all the callbacks.
  
  ```javascript
  function callbacks(foods){
    const lastCallback=`${"   ".repeat(foods.length+1)}done()`;

      const body = foods.reduceRight((a,c,i)=>{
        const tab = "    ".repeat(i);
        return `${tab}request(function () {
  ${a}
  ${tab}},"${c}")`;
      }, lastCallback)

    return (new Function(body));
  }
  ```
  The formatting is wonky because if I tabbed some of those lines `body` included the tab which messed up the formatting of the returned function.
   
  ## `new Function()`
  
  The function `callbacks(foods)` takes an array of food numbers, and returns a function that contains all `request()` calls with the nested callbacks. We are creating a function.
  
  I did this by creating `body`, a string that would become the body of the function. Then I found the `new Function()` constructor on this [stackoverflow thread](https://stackoverflow.com/questions/939326/execute-javascript-code-stored-as-a-string). Here's the [Mozilla Docs](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function) for `new Function()`. 
  
  Passing the string `body` into `new Function()` creates a function with `body` as the the body.
   
  For example, if this is the array:
   
  ```javascript
   
  const beef = "23557";
  const lettuce = "11251";
  const evoo = "04053";
  const cauli = "11135";
   
  let myFoods = [lettuce, evoo, cauli, beef]
  ```
  
  We could call `callbacks(myFoods)` which would return this function:
  
  ```javascript
  (function anonymous(
  ) {
  request(function () { //-----------------body start
      request(function () {
          request(function () {
              request(function () {
                 done()
              },"23557") //beef
          },"11135") //cauli
      },"04053") //evoo
  },"11251") //lettuce --------------------body end
  })
  ```
  
  If we call this function we get the food name from each request logged to the console:
  
  ```javascript
  callbacks(myFoods)()
  
  >Lettuce, cos or romaine, raw
  >Oil, olive, salad or cooking
  >Cauliflower, raw
  >Beef, ground, 95% lean meat / 5% fat, raw
  >done 
  ```
  
  ## Implementing the Search API
  
  I started to implement the search API so the user can search for food by name.
  
  I created a function, `searchURL()` that creates the URL we'll need. 
  
  ```javascript
  const searchURL = (searchTerm)=>{
        searchTerm = searchTerm.replace(" ","+");
        return `https://api.nal.usda.gov/ndb/search?q=${searchTerm}&ds=Standard%20Reference&format=json&api_key=${key}&sort=n&max=25&offset=0`;
  }
  ```
  
  Next I create the `search()` function. It makes our `XMLHttpRequest` using the search url returned from `searchURL(food)`.
  
  ```javascript
  const key = your_key;

  function search(callback,food){ 
  var xmlHttpRequest = new XMLHttpRequest();  
  xmlHttpRequest.open("GET", searchURL(food), true);  
  xmlHttpRequest.onreadystatechange = function () { 
      if (this.readyState == 4 && this.status == 200) {  
          console.log(JSON.parse(this.responseText).list.item.forEach((x)=>{console.log(x.name, " ", x.ndbno)}));  
          if (callback) {  
              callback();  
          }  
      }  
  };  
  xmlHttpRequest.send();  
  }
  ```
  
  If we run `search(done, "broccoli")` we get a long list of the names and NDB numbers of our results.
  
  ```javascript
  search(done,"broccoli")

  >Broccoli raab, raw   11096
  >Broccoli raab, cooked   11097   
  >Broccoli, raw   11090
  >Broccoli, frozen, chopped, unprepared   11092
  etc......
  >done
  ```

  This is where I left off. Bye bye!
  
  ![screenshot](log_imgs/ndb_4-7.gif)
  
- ## Thoughts and Feelings:
  This is fun. I wish I could work more on it but I know I should take a break and give my back some stretches.
  
  My log has been really helpful. It's a great resource to go back to when I forget something. Today, I forgot how to use `reduce()` but it was saved in my log.
  
  
## Day 96
### 4/6/19

- ## Recipe Calculator
  
  ## XHR Nested Callbacks
  
  Yesterday, I found this example of having [multiple serial asynchronous requests](https://www.thibaudlopez.net/xhr/generator/1.3/?language=jscallbacks&type=multiple&asynchronous=true&showResponseHandlers=false&responseHandler=inner&communication=serial). AKA callback hell. I don't know why, but the code uses the method `print()` where I think they meant to use `console.log()`. Probably because in other langauges `print` does the same thing as `console.log()` and maybe the person who made this was confused. But in JavaScript `print()` literally opens your print window so you can physically print something onto paper. 
  
  This worked but it's so long. How can we make it DRY?
  
  ```javascript
  const beef = "23557";
  const evoo = "04053";
  const cauli = "11135";

  const key = your_key_here;
  const url = (ndb)=>(`https://api.nal.usda.gov/ndb/V2/reports?ndbno=${ndb}&type=b&format=json&api_key=${key}`);

  function run() {  
      request1(function () {  
          request2(function () {  
              request3(function () {  
                  done();  
              });  
          });  
      });  
  }  
  function request1(callback1) {  
      // request 1  
      console.log("request1");  
      var xmlHttpRequest1 = new XMLHttpRequest();  
      xmlHttpRequest1.open("GET", url(beef), true);  
      xmlHttpRequest1.onreadystatechange = function () { 
          if (this.readyState == 4 && this.status == 200) {  
              // response 1  
              debugger;
              console.log("response1=" + JSON.parse(this.responseText).foods[0].food.desc.name);  
              // continue execution in the callback  
              if (callback1) {  
                  callback1();  
              }  
          }  
      };  
      xmlHttpRequest1.send();  
      console.log("line 52");
  }  
  function request2(callback2) {  
      // request 2  
      console.log("request2");  
      var xmlHttpRequest2 = new XMLHttpRequest();  
      xmlHttpRequest2.open("GET", url(evoo), true);  
      xmlHttpRequest2.onreadystatechange = function () {  
          if (this.readyState == 4 && this.status == 200) {  
              // response 2  
              console.log("response2=" + JSON.parse(this.responseText).foods[0].food.desc.name);  
              // continue execution in the callback  
              if (callback2) {  
                  callback2();  
              }  
          }  
      };  
      xmlHttpRequest2.send();  
  }  
  function request3(callback3) {  
      // request 3  
      console.log("request3");  
      var xmlHttpRequest3 = new XMLHttpRequest();  
      xmlHttpRequest3.open("GET", url(cauli), true);  
      xmlHttpRequest3.onreadystatechange = function () {  
          if (this.readyState == 4 && this.status == 200) {  
              // response 3  
              console.log("response3=" + JSON.parse(this.responseText).foods[0].food.desc.name);  
              // continue execution in the callback  
              if (callback3) {  
                  callback3();  
              }  
          }  
      };  
      xmlHttpRequest3.send();  
  }  
  function done() {  
      // end  
      console.log("done");  
  }  
  
  run()
  ```
  
  ## Making This Code Dry
  
  Ok I was very confused for a while and a bunch of things just clicked. I think. 
  
  I thought it was going to be hard to make the above code DRY because you can't pass arguments to callbacks. I thought in order to make this code DRY we need to pass arguments to the callbacks. But you don't. I was just very confused about where the callbacks start and end in this code. So I'll get back to how to actually make this code dry. But since I put some time into reviewing closures, I'll talk about that for a little even though this isn't necessary for our code.
  
  ## Passing Parameters to a Callback
  
  From [Passing arguments to callback functions](https://www.jstips.co/en/javascript/passing-arguments-to-callback-functions/):
  
  >By default you cannot pass arguments to a callback function
  
  You need closures to pass arguments to a callback.
  
  ## Closures
  
  I took a lynda tutorial on closures a few months back. At the time, I didn't grasp the concept. I don't think I had enough context for when you would need them to understand them. I'm still trying to get the idea, but I think I'm getting closer.
  
  >What are closures?
  >Closures are functions that refer to independent (free) variables. In other words, the function defined in the closure ‘remembers’ the environment in which it was created. Check MDN Documentation to learn more.
  
  *from [Passing arguments to callback functions](https://www.jstips.co/en/javascript/passing-arguments-to-callback-functions/)*
  
  ```javascript
  function callback(a, b) {
  return function() {
    console.log('sum = ', (a+b));
  }
  }

  var x = 1, y = 2;
  document.getElementById('someelem').addEventListener('click', callback(x, y));
  ```
  
  Here's my [codepen for this closure](https://codepen.io/Dashiee/pen/XQKQgo?editors=1111).
  
  I hope this helps me in the future. Right now I don't need this. Moving on.
  
  ## Making The Code DRY for Real
  
  OK. So I made the code DRY and got this code to work without closures and I was like wait what's going on? Why didn't I need the closure?
  
  ```javascript
  const beef = "23557";
  const evoo = "04053";
  const cauli = "11135";

  let data= {};

  const key = your_key;
  const url = (ndb)=>(`https://api.nal.usda.gov/ndb/V2/reports?ndbno=${ndb}&type=b&format=json&api_key=${key}`);

  function run() {  
      request(function () {  
          request(function () {
              request(function () {
                  done();
              }, evoo)
          }, cauli)
      }, beef)    
  }  

  function request(callback,food){ 
      var xmlHttpRequest = new XMLHttpRequest();  
      xmlHttpRequest.open("GET", url(food), true);  
      xmlHttpRequest.onreadystatechange = function () { 
          if (this.readyState == 4 && this.status == 200) {  
              console.log(JSON.parse(this.responseText).foods[0].food.desc.name);  
              if (callback) {  
                  callback();  
              }  
          }  
      };  
      xmlHttpRequest.send();  
  }  

  function done() {  
      // end  
      console.log("done");  
  }
  
  run()
  ```
  
  So, as you can see, you now have a request function that can be reused. `request` creates a specified url from the `food` parameter.
  
  ### `request()` Is Not The Callback
  
  I didn't understand how I could pass the params `callback` and `food` into the callback. But then it clicked, I ***wasn't*** passing params to the callback. I thought ***`request`*** was the callback. We ***are*** passing parameters to `request`. But request ***IS NOT*** the callback. `request` is ***inside*** our callbacks. ***CLICK! DING DING DING!***
  
  Our callbacks are the anonymous functions.
  
  ![screenshot](log_imgs/callbacks_4-6.PNG)
  
  Whoa. I was seeing this wrong. Now I see it correctly.
  
  ## Nested Calls For Lists of Foods
  
  We hardcoded in the foods we want nutrient reports for(beef, cauliflower, and EVOO). But how do we do this for a list of food NDBno's from somewhere else, for example from a list submitted by the user?
  
  ### What we have now:
  ```javascript
  function run() {  // What if we want to run different requests??
        request(function () {  
            request(function () {
                request(function () {
                    done();
                }, evoo)
            }, cauli)
        }, beef)    
    } 
  ```
  
  I think this is where promises come in extra handy. Not sure yet. But I'm going to try to do it without promises still.
  
  We'll need to create a function that takes an array of food numbers and outputs a custom version of what's currently in the body of our `run()` function. Maybe we can use `reduce()` for this.
  
  ## `reduceRight()`
  
  I'm gonna start with the innermost callback and build around it. I'll need to start with the last item in the array. We can use `reduceRight()` instead of `reduce()` to switch the order.
  
  This is where I left off. See you tomorrow!
  
- ## Thoughts and Feelings:
  Today was awesome. I love when something clicks. I was so confused. I can see the code better now. It's like I developed my javascript eye a little bit more. 
  
  Just to clarify, I'm learning to do a callback hell so I can better understand why they aren't a good idea. I think this will really deepen my undertstanding when I learn how to do the modern requests: async/await and promises. 
  
  I really like learning on my own because I like controlling what I learn and when. In school, you must stick to the curriculum. But I like going with what *I need* to learn. Of course, sometimes you don't know what you need. But that's where it's helpful to ask someone more experienced.

## Day 95
### 4/5/19

- ## Recipe Calculator

  I started a new project, which I wrote about yesterday. It still uses the NDB API. This one takes a recipe and recalculates the ingredients to fit your macros. I don't expect to finish this soon, it sounds hard. But I will use it first to get more comfortable with asynchronous functions. I also want to get an idea of how these calculations for the ingredients would work.
  
  ## NDB Search API
  
  I used the Search API to search for food products.
  
  Here's the [NDB API Searches documentation](https://ndb.nal.usda.gov/ndb/doc/apilist/API-SEARCH.md)
  
  Here's a search for ground beef:
  
  `https://api.nal.usda.gov/ndb/search/?format=json&q=ground+beef&ds=Standard%20Reference&sort=n&max=25&offset=0&api_key=DEMO_KEY`
  
  ### Important Parameters:
  - Search Terms
    - q=ground+beef 
  - Data source. Must be either 'Branded Food Products' or 'Standard Reference'
    - ds=Standard%20Reference
  
  ***It would be cool in the recipe app to use these searches to add ingredients to a recipe.***
  
  For now I'm going to just save some NDB numbers for different ingredients. Yesterday, I already grabbed some so I'll use those.
  
  
   ### Ingredients and NDBno
  
  - Beef, ground, 95% lean meat / 5% fat, raw: **23557**
  - Lettuce, cos or romaine, raw: **11251**
  - Oil, olive, salad or cooking: **04053**
  - Cauliflower, raw:  **11135**
  
  The numbers should be *strings* in the app, otherwise funny things can happen. Olive oil, when not a string, didn't work. I think it has to do with it starting with a 0. The request coming back failed and said `{error: "No data for ndbno 2091"}`. I don't know how it got `2091` from 04053. Saving 04053 as string fixed the issue: `const evoo = "04053";`

  ## Ordering Callbacks, Callback Hell
  
  I can see now how a developer could land themselves in callback hell. Callback hell is when you have a callback within a callback within a callback etc. I didn't understand why you would do that instead of just doing a bunch of requests or callbacks, unnested. But [Khawar Jatoi](https://twitter.com/khawar_jatoi) told me it's because sometimes you need to control the order of how things load. When you have multiple requests unnested, you don't know when each will complete. The order could be off as it was here:
  
  ```javascript
  const beef = "23557";
  const lettuce = "11251";
  const evoo = "04053";
  const cauli = "11135";

  let data= {};

  const key = your_key;

  function makeRequest(ndbNumber){
  const url = `https://api.nal.usda.gov/ndb/V2/reports?ndbno=${ndbNumber}&type=b&format=json&api_key=${key}`;

      let xhr = new XMLHttpRequest();

      xhr.onreadystatechange = function (){
          if (this.status == 200 && this.readyState == 4){
              const resp = JSON.parse(this.responseText);
              callback(resp);
          }
      }

      xhr.open("GET",url,true);
      xhr.send();
  }

  function callback(resp){
      const foodName = resp.foods[0].food.desc.name;
      data[foodName] = resp;
      console.log(foodName);
  }    
  
  //_________________________The objects returned from the requests below return in different orders_______________
  makeRequest(beef);
  makeRequest(lettuce);
  makeRequest(evoo);
  makeRequest(cauli);
  ```
  
  An example of one of the orders in which I got my objects back.
  
  ![screenshot](log_imgs/xhr_4-5.PNG)
  
  ## XMLHttpRequest Callback Hell
  
  I want to put my code into XHR callback hell format. Obviously, this isn't something we are going to strive for. We're supposed to use other methods like Promises and async/await to *avoid* callback hell. However I'm trying to learn, so I want to get familiar with callback hell.
  
  In my project order doesn't matter so much, but I want to see how to control it anyways.
  
  I had trouble getting my code into nested callbacks. It seemed like I did get it to work at one point but there were somethings that were off and I was confused.
  
  I found this example of having [multiple serial asynchronous requests](https://www.thibaudlopez.net/xhr/generator/1.3/?language=jscallbacks&type=multiple&asynchronous=true&showResponseHandlers=false&responseHandler=inner&communication=serial), I guess another way of saying nested callbacks or callback hell. I also found the term "chained XMLHttpRequests".
  
  I didn't get mine too work yet.

- ## Thoughts and Feelings:

  I thought what I was learning about today was challenging. I can't wait until I can understand all this. I feel a lot closer than before though. 
  
  I was kind of hungry when coding. Maybe if I have a snack break, I'd code better. I usually eat before and after coding. One thing I won't do it eat while coding. Mindless eating will make you gain weight and it keeps you from fully enjoying your food. That the worst way to get fat. What's the point in getting fat if you don't even get to enjoy your food? When I eat I like to fully experience and enjoy my food because I love foooood.
  
  Today I co-worked, again, at Emerald Tavern Games and Cafe in Austin.
  
  ![screenshot](log_imgs/cowork_4-5.jpg)
  

## Day 94
### 4/4/19

- ## USDA Food Composition Database App
  
  ## CSS Issue
  I had this CSS issue. The Submit Button didn't align with the input.
  
  <img src="log_imgs/css_4-3.png" width="250" />
  
  ### Solution:
  
  I this was just the browsers style sheet adding padding to the top.
  
  <img src="log_imgs/css_4-4.PNG" width="250" />
  
  ### Solution:
  
  So I added `padding:0;` to my style sheet to override the user agent stylesheet.
  
  ## Hiding The API Key
  
  Fellow #100DaysOfCoder [Evan Burnell](https://twitter.com/ev_burrell) suggested using [dotenv](https://t.co/DOuoLuePMZ).
  
  I found [this tutorial by Dev Coffee](https://www.youtube.com/watch?v=zDup0I2VGmk). It looked simple enough. I was intimidated at first by the backend/npm stuff. But It doesn't look too hard. I already have npm set up from when I took a tutorial on it on lynda.com.
  
  I didn't follow the tutorial yet. 
  
  ## Switching Gears
  
  So I know I keep starting apps and not finishing them. But, I feel like instead of setting up dotenv, I really should stick to what I need to learn: async/await, promises. 
  
  I also am super curious about playing with this other app idea I've had for awhile that also has to do with the USDA NDB API. It would take a recipe and users daily macros, and fit the recipe to their macros. It seems hard to me. So I just want to play around with the NDB API in the console and see if I can get a clearer idea of what I need.
  
  ## Playing More with NDB API
  
  ## Lists
  
  I wanted to play around with getting foods by the highest value for a nutrient. We have to get the list of nutrient codes first, because we'll need the code for the nutrient we want to pass into the params. 
  
  Here's the [NDB API Lists documentation](https://ndb.nal.usda.gov/ndb/doc/apilist/API-LIST.md)
  
  We need the param `lt=n` which means list type = nutrients. You can also get lists of other things like food groups. We'll also need to raise the max results, which defaults to 50. There are 196 nutrients in this list which you can see in the return response: `"total": 196`. But the max results defaults to 50. So we add the param `max=196'
  
  https://api.nal.usda.gov/ndb/list?format=json&max=196&lt=n&api_key=DEMO_KEY
  
  Now I can `cmd + f` to find the listing for the nutrients I want to play with. For example, protein is 203.
  
  ## Nutrient Reports

  Let's say I want to retrieve a bunch of foods with the highest iron. I need a nutrient report.
  
  Here's the [Nutrient Report documentation](https://ndb.nal.usda.gov/ndb/doc/apilist/API-NUTRIENT-REPORT.md).
  
  Iron's nutrient id is 205. If you do a nutrient search for any nutrient and the argument `sort=c`, to sort by nutrient content, you get two problems:
  
  - bunch of results, too many. 
  - No standard weight: The results aren't organized by value per 100g. Instead it sorts it by value for other weights. Which makes comparisons hard.
   
  To reduce the number of results I added food groups to the parameters. You can use lists to get a list of food groups:
  
  
  **Food Groups List:** https://api.nal.usda.gov/ndb/list?format=json&lt=g&api_key=DEMO_KEY
  
  You can only use 10 groups in your search so I went with the one most relavant to keto paleo, since that's what my app with deal with:
  
  ### Relevant Food Groups
  - Beef Products: 1300
  - Dairy and Egg Products: 0100
  - Fats and Oils: 0400
  - Finfish and Shellfish Products: 1500
  - Fruits and Fruit Juices: 0900
  - Lamb, Veal, and Game Products: 1700
  - Nut and Seed Products: 1200
  - Pork Products: 1000
  - Poultry Products: 0500
  - Vegetables and Vegetable Products: 1100
  
  
  #### These also seemed MAYBE relevant but I didn't have enough room:
  - Sausages and Luncheon Meats: 0700
  - Spices and Herbs: 0200
  - American Indian/Alaska Native Foods: 3500
  - Legumes and Legume Products: 1600
  
  #### Here's the search for all these food groups for iron:
  
  https://api.nal.usda.gov/ndb/nutrients/?format=json&api_key=DEMO_KEY&nutrients=205&sort=c&fg=1300&fg=0100&fg=0400&fg=1500&fg=0900&fg=1700&fg=1200&fg=1000&fg=0500&fg=1100measureby=g

  ### No Standard Weight Problem
  
   If you use USDA Food Composition Databases's [nutrient search](https://ndb.nal.usda.gov/ndb/nutrients/), you can specify to *measure by 100g*. I wondered if there's an equivilet param for the api. The param in the search is `measureby=g`. But when I add that to my api url:

  https://api.nal.usda.gov/ndb/nutrients/?format=json&api_key=DEMO_KEY&nutrients=205&sort=c&fg=1300&measureby=g

  ***VS.*** 

  https://api.nal.usda.gov/ndb/nutrients/?format=json&api_key=DEMO_KEY&nutrients=205&sort=c&fg=1300

  **It makes no difference.**
  
  What we can do is sort the objects after we retrieve them by **gm** instead of **value**. `sort=c` sorts the list by value. From the [documenntation for the NDB API Nutrient Reports](https://ndb.nal.usda.gov/ndb/doc/apilist/API-NUTRIENT-REPORT.md). 
  
  **value:** Value of the nutrient for this food
  
  **gm:**	The 100 gram equivalent value for the nutrient 
  

  ## Playing With a Sample Recipe
  I realized I might not really need the nutrient reports for what I want to do. I decided to start playing around with a sample recipe.
  
  I got the NDB numbers for some ingredients for a basic salad.
  
  ### Ingredients and NDBno
  
  - Beef, ground, 95% lean meat / 5% fat, raw: **23557**
  - Lettuce, cos or romaine, raw: **11251**
  - Oil, olive, salad or cooking: **04053**
  - Cauliflower, raw:  **11135**
  
  Tomorrow, I'll play with these. 
  
- ## Thoughts and Feelings:

  I'm kind of excited to play with this tomorrow! I think since I'll be grabbing more than one food, this might require multiple asynchronous functions. This would help me understand promises, async, await, etc. But I'm excited because it's part of an app idea I really want to make.
  
  Today I co-worked at Emerald Tavern Games and Cafe in Austin.
  
  ![screenshot](log_imgs/co-work_4-4.jpg)
  
  

### 4/3/19

- ## USDA Food Composition Database App
  ## Using Regex When You Don't Know Regex
  
  I use regex a lot but I don't actually know regex very well.
  
  ## This is my process for using regex:
  
  1. **Google** the regex I want or something similar. 
     - Ex: "***regex zeros after decimal***"
  2. **Find some regex pattern to try out**, usually on stackoverflow. 
     - Ex: [Using RegEx how do I remove the trailing zeros from a decimal number](https://stackoverflow.com/questions/26299160/using-regex-how-do-i-remove-the-trailing-zeros-from-a-decimal-number)
     
  3. **Go to [regexr.com](https://regexr.com/)** to test your regex and play around with it. 
  4. Put the suggested Rexeg in the **Expression** field, replacing \[A-Z]\w+.
  
     - ![screenshot](log_imgs/rgx1_4-2.png)
  
  5. Put the text you want to test in the **Text** area.
  
     - ![screenshot](log_imgs/rgx2_4-2.png)
     
  6. Keep playing with the regex, googling additional help, etc until it highlights what you want.
  
  #### Warning: 
  
  There was one time when regexr.com didn't recognize some pattern that worked on another site. Check multiple regex tester sites if you get stuck.

  ## Remove Trailing Zeros
  
  I thought I should remove trailing zeros (***ex:*** 0.**000** to 0) with a regex and `.replace()`. But I couldn't find any regex that worked and I don't know regex well enough to figure it out. So I google "javascript remove trailing 0" and found [this](https://stackoverflow.com/questions/3612744/remove-insignificant-trailing-zeros-from-a-number) stackoverflow thread. 
  
  ### Remove trainig zeros
  
  ```javascript
  var n = 1.245000
  var noZeroes = n.toString() // "1.245"
  ```
  It works the other way too.
  ```javascript
  var n = "1.245000"
  var noZeroes = parseFloat(n) // "1.245"
  ```
  
  ## CSS Funk
  
  I'm not sure why the submit button isn't aligning with the input:
  
  ![screenshot](log_imgs/css_4-3.png)
  
  ## To Do
  
  I did a lot of css today, and some code to format the labelVal to be rounded and without trailing zeros.
  
  ### Still Left
  
  - How to hide API key? can I do it without a backend?
  - Css fixes
  - PWA
  - word food search
  - try with promises, async/await
  
  ![screenshot](log_imgs/ndb_4-3.gif)
  
- ## Thoughts and Feelings:

  I didn't respect my stretch breaks. I need to do that! I'm feeling the effects.
  
  I went to a coworking meetup at Orange Co-Working in Austin.
  
  ![screenshot](log_imgs/orange_4-3.png)

## Day 92
### 4/2/19

  I accidently deleted a bunch of entries on my log some how. So I went back to an old commit and copied them back in.
  
- ## USDA Food Composition Database App
  ## DevTools Color Picker
  
  To use the color picker in dev tools, find a color square in the styles panel and click it.
  <img src="log_imgs/colorsquare_4-2.png"  width="250" />

  
  The color picker window should pop up. If the dropper is blue it means the color picker is activated. Hover over different colors in your browser window and click on the color you want to get the hex number. This also changes the color value for the property you originally clicked on when you clicked the color square.
  
  <img src="log_imgs/colorpicker_4-2.png"  width="250" />
  
  ## Design
  
  I want to model my design after the MyFitnessPal design so it's easy to look back and forth between my app and MyFitnessPal.
  
  <img src="log_imgs/mfp2_3-31.PNG"  width="250" /> <img src="log_imgs/mfp3_3-31.PNG"  width="250" /> <img src="log_imgs/mfp4_4-2.PNG"  width="250" />
  
  ### MyFitnessPal Colors
  
  I used the color picker to find these colors with dev tools. I used these image files that are hosted here on githib.
  
  - Blue bar: #0071bb
  - Gray bar: #efeff4
  - Thin gray border: #d1d5da
  - "Create Food": #ffffff
  - Check: #7eb8de
  - "Nutrtion Facts": #333333
  - Nutrient Text: #343434
  - "Optional": #c7c7cc
  - Background: #ffffff
  - Typing text: ##0071bb
  - Previously entered text: #6a6a6a
  
  ## Layout
  
  I used this codepen to get ideas for how to position the label.
  
  https://codepen.io/scissorsneedfoodtoo/pen/MxVpLK
  
  ## CSS So Far
  
  This is what I have so far
  
  <img src="log_imgs/css_4-2.PNG"  width="300" />
  
  ## Bye Bye
  
  I need to sign off, sorry about any mispellings- computer running out of power.

## Day 91
### 4/1/19

- ## Why I'm Quiting Coding Forever

  I'm not! April Fools.
  
  OK back to work

- ## Github Name

  I finally changed my github screenname from my old first grade AOL instant messenger account name to a more professional screenname. I didn't mind the cute screenname it's just that I've been using that screenname since I was 7 and I don't know what sort of crazy comments I've contributed to the internet since that time, let alone since last week. I'd rather not associate my professional identity with the crazy things I've posted on the internet in the last 21 years. And if you haven't posted crazy things on the internet in 21 years, then live a little and take some risks for your own sake! Just kidding.
  
- ## USDA Food Composition Database App
  ## Calculations

  Below I gathered together the information we need to figure out the calculations for the nutrient label. The values for USDA DV's are from [here on nih.gov](https://www.dsld.nlm.nih.gov/dsld/dailyvalue.jsp).
    
  | Nutrient            | MyFitnessPal Unit | USDA DV for % | DV unit | NDB unit |   |
  |---------------------|-------------------|---------------|---------|----------|---|
  | Calories            | kcal              |               |         | kcal     |   |
  | Total Carbs         | g                 |               |         | g        |   |
  | Protein             | g                 |               |         | g        |   |
  | Total Fat           | g                 |               |         | g        |   |
  | Saturated Fat       | g                 |               |         | g        |   |
  | Polyunsaturated Fat | g                 |               |         | g        |   |
  | Monounsaturated Fat | g                 |               |         | g        |   |
  | Trans Fat           | g                 |               |         | g        |   |
  | Cholesterol         | mg                |               |         | mg       |   |
  | Sodium              | mg                |               |         | mg       |   |
  | Potassium           | mg                |               |         | mg       |   |
  | Dietary Fiber       | g                 |               |         | g        |   |
  | Sugars              | g                 |               |         | g        |   |
  | Vitamin A           | %                 | 900           | RAE mcg | RAE mcg  |   |
  | Vitamin C           | %                 | 90            | mg      | mg       |   |
  | Calcium             | %                 | 1300          | mg      | mg       |   |
  | Iron                | %                 | 18            | mg      | mg       |   |
  | Added Sugars        | g                 |               |         | N/A      |   |
  | Vitamin D           | %                 | 20            | mcg     | IU       |   |
  | Sugar Alcohols      | g                 |               |         | N/A      |   |
  
  We'll need to do calculations for the nutrients that require percentages: 
  - Vitamin A
  - Vitamin C
  - Calcium
  - Iron
  - Vitamin D
  
  We'll also need to do calculations for any nutrient that's NDB value unit is different from MyFitnessPal's units or the DV units. The only one that applies to is **Vitamin D** which we'll need to convert from UI to mcg.
  
  - Vitamin D
    - The conversion for Vitamin D is [1 IU = 0.025 mcg.](https://dietarysupplementdatabase.usda.nih.gov/Conversions.php)
  
  ### Calculate the Percentages
  
  | Nutrient    | Calculation             |
  |-------------|-------------------------|
  | Vitamin A % | NDB value / 900         |
  | Vitamin C % | NDB value / 90          |
  | Calcium %   | NBD value / 1300        |
  | Iron %      | NBD value / 18          |
  | Vitamin D % | (NDB value * .025) / 20 |
  
  ## Regex: camelCase to Label
  
  I want to take the property of a value, which is written in camelCase, and use the property to create a displayable version for the user. 
  
  - ex. sugarAlcohols --> Sugar Alcohols
  
  I found this [stackoverflow thread](https://stackoverflow.com/questions/15369566/putting-space-in-camel-case-string-using-regular-expression) on how to add the space.
  
  ```javascript
  var rex = /([A-Z])([A-Z])([a-z])|([a-z])([A-Z])/g;

  "CSVFilesAreCoolButTXT".replace( rex, '$1$4 $2$3$5' );
  ```
  
  Then I found this [stackoverflow thread](https://stackoverflow.com/questions/1026069/how-do-i-make-the-first-letter-of-a-string-uppercase-in-javascript) on how to capitalize the first letter.
  
  ```javascript
  function capitalizeFirstLetter(string) {
    return string.charAt(0).toUpperCase() + string.slice(1);
  }
  ```  
  ## Working
  
  I got my app working!
  
  ![screenshot](log_imgs/NBD_4-1.gif)
  
  I still need to: 
  - add css
  - double check this is all accurate, unit tests?
  - make into pwa
  
- ## Thoughts and Feelings:

  Spent extra time on this today, so I'm not going to post the work to save time. Plus I need to hide the key and I don't feel like figuring that right out.
  
## Day 90
### 3/31/19

- ## 100Daysofcode Tweet Search Project
  I'm taking a break from my twitter app project to play with the USDA NDB API to get better at working with api's and asynchronicity.

- ## USDA Food Composition Database App

  I spent a lot of time researching how US nutrition labels work. So a lot of this info may be boring to you if you read my log to see my *coding* journey. However, I really wanted to get this information down because it's surprisingly difficult to find and it may help others who are interested in nutrition and/or coding.

  ## Manually Search For a Food Item
  
  This is where you search food items on the USDA database website.
  
  - https://ndb.nal.usda.gov/ndb/search/list
  
  This page will help us for this project to find the corresponding NBD number for each food item, although, you *can* do this with the api. I will learn how to do it with the API later.

  ## Serving Size
  
  The user needs to know the serving size when they enter a new food on MyFitnessPal.
  
  <img src="log_imgs/mfp1_3-31.PNG"  width="200" />
  
  ### 100 gram serving
  I found out on the [documentation for the NBD food reports](https://ndb.nal.usda.gov/ndb/doc/apilist/API-FOOD-REPORTV2.md) that the nutrients listed are for an **100 gram serving** of each food item. I'll also need this info later if I'm going to give the user the option to get different serving sizes.
  
  ## Nutrition Label Daily Values
  
  ### Daily Values:
  
  > Recommended intakes of nutrients vary by age and gender and are known as Recommended Dietary Allowances (RDAs) and Adequate Intakes (AIs). However, one value for each nutrient, known as the Daily Value (DV), is selected for the labels of dietary supplements and foods. 
  
  *[Daily Values, nih.gov](https://ods.od.nih.gov/HealthInformation/dailyvalues.aspx)*
  
  To calculate the percentages for the labels, I need to know the total **daily values** for each nutrient on US nutrition labels.
  
  - For example, there's 0.73mg of iron in broccoli. *We need to know how many mg of iron make 100% of the daily value on food labels.* The answer is *18mg* of iron is *100%*. 0.73mg is ***4%*** of 18mg. So 4% iron would be the percent value for the USDA label.
  
  ### Where Can We Find The Label DV's?
  
  There are old label daily values, and new ones which will be enforced [by January 1st, 2020 for large companies](https://en.wikipedia.org/wiki/Reference_Daily_Intake#Food_labeling_reference_tables). I'm going to go with the **new values.**
  
  I'm a little confused, but I believe the correct values for the new labels are in the "Adults and Children >=4 years" column [on this table](https://www.dsld.nlm.nih.gov/dsld/dailyvalue.jsp). There's also a table [here on wikipedia](https://en.wikipedia.org/wiki/Reference_Daily_Intake#Food_labeling_reference_tables) but it has separate values for men and women, and I'm not sure which side to go with to match US food labels. There's yet another table [here on wikipedia](https://en.wikipedia.org/wiki/Nutrition_facts_label#United_States) which has different units and I can't tell if the right-hand column is meant to be the new label values or not. So I'm going to go with the [NIH page](https://www.dsld.nlm.nih.gov/dsld/dailyvalue.jsp) since it's on a goverment website and that seems more legit. I don't know why this was so hard to figure out.

  #### I'm Using These Values:
 
  - **Adults and Children >=4 Years Column** [NIH page](https://www.dsld.nlm.nih.gov/dsld/dailyvalue.jsp)


  ## What Info Does MyFitnessPal Request?
  
  Here you can see what info MyFitnessPal asks for, which pretty much corresponds to what you'd see on a **USDA food label**.
  
  <img src="log_imgs/mfp2_3-31.PNG"  width="250" /> <img src="log_imgs/mfp3_3-31.PNG"  width="250" />
  
  ### Nutrients
  - Calories
  - Total Carbs (g)
  - Protein (g)
  - Total Fat (g)
  - Sat Fat (g)
  - Polyunsat Fat (g)
  - Monounsat Fat (g)
  - Trans Fat (g)
  - Cholesterol (mg)
  - Sodium (mg)
  - Potassium (mg)
  - Fiber (g)
  - Sugars (g)
  - Vitamin A (%)
  - Vitamin C (%)
  - Calcium (%)
  - Iron (%)
  - Added Sugar (g)
  - Vitamin D (%)
  - Sugar Alcohols (g)
  
  ## Vitamin D Confusion
  
  In the API for each food item, there are *two* objects containg the vitamin d information. One has the property value **"name":"Vitamin D"**. The other is **"name":"Vitamin D (D2 + D3)"**. I'm not sure which goes in food labels.
  
  ### Use "name":"Vitamin D"
  
  I think we use the **"name":"Vitamin D"** object instead of **"name":"Vitamin D (D2 + D3)"**. 
  
  But the only reason I think this is because on [this government page about vitamin d](https://ods.od.nih.gov/factsheets/VitaminD-HealthProfessional/), in the table **"Table 3: Selected Food Sources of Vitamin D"**, you can see it shows that a large egg has 41 UI of Vitamin D which is 10% of the DV. When we go to the [NBD for eggs](https://ndb.nal.usda.gov/ndb/foods/show/01123?fgcd=&manu=&format=&count=&max=25&offset=&sort=default&order=asc&qlookup=Egg%2C+1+large&ds=SR&qt=&qp=&qa=&qn=&q=&ing=) we see that a large egg has 41 UI of ***Vitamin D*** and 1.0 µg of ***Vitamin D (D2 + D3)*** . So it looks like the 41 UI of ***Vitamin D*** is the value being referenced ***not*** the 1.0 µg of ***Vitamin D (D2 + D3)*** value.
  
  ### UI vs mcg
  
  To complicate things futher, the new labeling will require vitamin D to be labeled in mcg, not UI. Because of this, our [reference table for DV's](https://www.dsld.nlm.nih.gov/dsld/dailyvalue.jsp) listes 20mcg for vitamin D. But in the database, the values for Vitamin D are in UI. But the conversion is straight forward. 
  
  The conversion for Vitamin D IU is	1 IU = 0.025 mcg. This is not the same for all vitamins apparently. [See here.](https://dietarysupplementdatabase.usda.nih.gov/Conversions.php)
  
  ## Vitamin A Confusion
  
  There are also two listings for vitamin A, the object with **"name":"Vitamin A, RAE"** and **"name":"Vitamin A, IU"** .
  
  I believe we go with the **"name":"Vitamin A, RAE"** object, because on our [reference table](https://www.dsld.nlm.nih.gov/dsld/dailyvalue.jsp) the unit listing for vitamin A is **"Micrograms RAE (mcg)"** . Additionally, in [this conversion guide for the nutrient units](https://dietarysupplementdatabase.usda.nih.gov/Conversions.php) the new unit requirement for vitamin A is **"mcg RAE"**.  
  
  ### Vitamin A Unit Conversion
  
  **µg**: This funky symbol means microgram (mcg). That's the unit listing for **"name":"Vitamin A, RAE"**.
  
  The [conversion guide for the nutrient units](https://dietarysupplementdatabase.usda.nih.gov/Conversions.php) for the new label requirements show these conversions for vitamin A:
  
  - 1 mcg RAE = 1 mcg retinol
  - 1 mcg RAE = 2 mcg supplemental beta-carotene
  - 1 mcg RAE = 12 mcg beta-carotene
  - 1 mcg RAE = 24 mcg alpha-carotene
  - 1 mcg RAE = 24 mcg beta-cryptoxanthin
  
  So I'm not totally sure what's going on here. I think it's ***1:1*** for the **"name":"Vitamin A, RAE"** listing because it's already giving us micrograms of RAE. I think these conversions are not necessary for us.
  
  ## JSON Object Style Guide
  
  I'm making a JSON object that contains all the nutrients for a given food item. I always forget whether JSON properties should be snake_case or camelCase. [This stackoverflow thread](https://stackoverflow.com/questions/5543490/json-naming-convention) answered my question:
  
  >In this document Google JSON Style Guide (recommendations for building JSON APIs at Google),
  >
  >It recommends that:
  > 1. Property names must be camelCased, ASCII strings.
  > 2.  The first character must be a letter, an underscore (_) or a dollar sign ($).

  Here's the [guide they're referencing](https://google.github.io/styleguide/jsoncstyleguide.xml?showone=Property_Name_Format#Property_Name_Format).
  
  ## App So Far
  
  So far I'm able to grab all the values I need for a given NDB number:
  
  <img src="log_imgs/NBD_3-31.PNG"  width="200" />
  
- ## Thoughts and Feelings:

  I spent so long just figuring out all this nutrition label info. So I'm not going to post my work for today, just to save time. It's already almost time for dinner and I should probably still do a little more actually coding if I have time. I'm so glad I got all this info down because it's been really annoying to find in the past year of my food logging.


## Day 89
### 3/30/19
- ## **Promises**
  
  I continued watching the videos recommended to me by [Khawar Jatoi](https://twitter.com/khawar_jatoi) to learn more about promises. Today, I watched the second video, [Promises - Part 8 of Functional Programming in JavaScript](https://www.youtube.com/watch?v=2d7s3spWAzo&vl=en). This is the list of videos Khawar sent me to watch ***in order***:

  
  1. [What the heck is the event loop anyway? | Philip Roberts | JSConf EU](https://www.youtube.com/watch?v=8aGhZQkoFbQ)
  2. [Promises - Part 8 of Functional Programming in JavaScript](https://www.youtube.com/watch?v=2d7s3spWAzo&vl=en)
  3. [async / await in JavaScript - What, Why and How - Fun Fun Function
](https://www.youtube.com/watch?v=568g8hxJJp4)

  Below are my notes on the *second video* and definitions I had to look up. I recommend watching the video and not relying on my notes, as these notes do not explain everything about promises. The notes are complementary definitions as well as concepts I felt like I needed to write down for my own understanding.

  ## Promises Video Notes
  
  **Composable:** "A composable function should have 1 input argument and 1 output value." *[hackernoon](https://hackernoon.com/javascript-functional-composition-for-every-day-use-22421ef65a10)*
  
  >*Function composition is a mathematical concept that allows you to combine two or more functions into a new function.*
  
  *[hackernoon](https://hackernoon.com/javascript-functional-composition-for-every-day-use-22421ef65a10)*
  
  >Promises serve that same purpose as callbacks do but promises are a bit more powerful because they are, unlike callbacks, composable.
  
  *[Promises - Part 8 of Functional Programming in JavaScript](https://www.youtube.com/watch?v=2d7s3spWAzo&vl=en)*
  
  ## Callback Hell, Why Have Nested Callbacks?
  
  In the tutorial, the instructor uses [this example to illustrate callback hell](https://youtu.be/2d7s3spWAzo?t=240), which happens when you want more than one image in this example:
  
  ```javascript
  import loadImageCallbacked
  from '.load-image-callbacked'

  let addImg = (src) => {
    let imgElement = 
      document.createElement('img')
    imgElement.src = src
    document.body.appendChild(imgElement)
  }

  loadImageCallbacked('images/cat1.jpg', (error,img1) => {     //------callback helll
    addImg(img1.src)
    loadImageCallbacked('images/cat2.jpg', (error,img2) => {
      addImg(img2.src)
      loadImageCallbacked('images/cat3.jpg', (error,img2) => {
        addImg(img3.src) 
      })
    })
  })
  ```
  
  I don't understand why you would do the above code to get multiple images instead of doing separate asynchronous functions that aren't nested like this:
  
  ```javascript
  loadImageCallbacked('images/cat1.jpg', (error,img1) => {
    addImg(img1.src)
  })

  loadImageCallbacked('images/cat2.jpg', (error,img2) => {
    addImg(img2.src)
  })

  loadImageCallbacked('images/cat3.jpg', (error,img2) => {
    addImg(img3.src)
  })
  ```
  
  I think it might have something to do with the event loop and what order these are called in, but I haven't wrapped my head around it yet.
  
  ### So what's the answer?
  
  I asked Khawar for help. He explained that in the nested version, it's guaranteed that the images load in order. But in my non-nested version you can't gaurentee the images will load in order.
  
  ## Moving On For Now
  
  I still have a bit to understand about promises, but I wanted to take a break from watching videos and do some coding. 
  
  I decided to start a food app I'd been meaning to work on for while. It will compile the **nutritional label values** for different foods by grabbing the **nutrient values from the USDA database** using their API. 
  
- ## USDA Food Composition Database App
  
  For those who aren't familiar with food tracking, the nutrient values on the **USDA database** *are not the same* as what you would see on the **nutritional labels** since the labels use a lot of percentages and the database uses value like 2mg, 300UI, etc. 
  
  I track my food intake on myfitnesspal.com. I often use the USDA database to get my nutrient values, but I have to do a lot of annoying math to convert the database values to percentages to enter into myfitnesspal. This app will take care of that math and do it for you.

  ## Nutrient Database API
  
  I'll be using the Nutrient Database API so this project will familiarize me with asynchronous functions and promises.
  
  I got my [NBD API key here](https://data.nal.usda.gov/dataset/composition-foods-raw-processed-prepared-usda-national-nutrient-database-standard-referen-15). Go down to the section called "Gaining Access".
    
  The documentation for the NBD can be found [here](https://ndb.nal.usda.gov/ndb/doc).
  
  ## Food Reports
  There are different types of reports you can get, but I will mostly need the food reports for my project.
  
  > A Food Report is a list of nutrients and their values in various portions for a specific food.
  
  *[What is a Food Report Version 2?](https://ndb.nal.usda.gov/ndb/doc/apilist/API-FOOD-REPORTV2.md)*
  
  The documentation for food reports is [here](https://ndb.nal.usda.gov/ndb/doc/apilist/API-FOOD-REPORTV2.md).
  
  With a food report I can look up a food by it's NBD number, let's say chinese cabbage which has a NBD number of 11119 which I happen to remember off the top of my head. Then you can grab the information for each nutrient: calories, iron, vitamin c, etc...
  
  I played around with traversing the JSON that was returned from this XMLHttpRequest:
  
  ```javascript
  
  let test = document.querySelector("#test");
  var url = "https://api.nal.usda.gov/ndb/V2/reports?ndbno=01009&ndbno=01009&ndbno=45202763&ndbno=35193&type=b&format=json&api_key=<your_API_Key_goes_here>";
  
  var xmlhttp = new XMLHttpRequest();

  xmlhttp.onreadystatechange = function() {
  if (this.readyState == 4 && this.status == 200) {
      var resp = JSON.parse(this.responseText);
      console.log(resp)
      myFunction(resp);
   }
  };
  xmlhttp.open("GET", url, true);
  xmlhttp.send();

  function myFunction(data) {

  document.getElementById("test").innerHTML = data;
  }
  ```
  
  I still have a bunch to do.
  
- ## Thoughts and Feelings:

  I liked this quote I came across:
  
  >A teacher’s failure to properly provide good real world examples will result in a student’s failure to understand why. 
  
  *[hackernoon](https://hackernoon.com/javascript-functional-composition-for-every-day-use-22421ef65a10)*
  
  It made me realize that I need to make sure I go out and find examples or ask questions that can illustrate why we do certain things in our code.

## Day 88
### 3/29/19
- ## **100Daysofcode Tweet Search Project**

  My peer on twitter, [Khawar Jatoi](https://twitter.com/khawar_jatoi), sent me some great links to learn more about promises. He sent me these three videos to watch ***in order***:
  
  1. [What the heck is the event loop anyway? | Philip Roberts | JSConf EU](https://www.youtube.com/watch?v=8aGhZQkoFbQ)
  2. [Promises - Part 8 of Functional Programming in JavaScript](https://www.youtube.com/watch?v=2d7s3spWAzo&vl=en)
  3. [async / await in JavaScript - What, Why and How - Fun Fun Function
](https://www.youtube.com/watch?v=568g8hxJJp4)

  Today, I watched the first video. It's only 27 minutes but it took me 1 hour and 45 minutes to go through it while taking notes and looking up words I didn't know. But wow! I understand how javascript works soo much better. 
  
  Below are my notes on the video and definitions I had to look up. I recommend watching the video and not relying on my notes, as these notes do not explain everything about event loops. The notes are complementary definitions as well as concepts I felt like I needed to write down for my own understanding.
  
  ## Event Loops Video Notes
  
  ## Definitions
  
  **JavaScript runtimes:** "JavaScript runtimes (like V8) have a heap (memory allocation) and stack (execution contexts). But they don’t have setTimeout, the DOM, etc. Those are web APIs in the browser." 
  
  *-from [JavaScript's Call Stack, Callback Queue, and Event Loop](http://cek.io/blog/2015/12/03/event-loop/)*
  
  ![screenshot](http://cek.io/images/event-loop/javascript.png)

  *-image from [JavaScript's Call Stack, Callback Queue, and Event Loop](http://cek.io/blog/2015/12/03/event-loop/)*
  
  **Runtime System:** "...also called run-time system, runtime environment or run-time environment, primarily implements portions of an execution model." *-[Runtime system - Wikipedia](https://en.wikipedia.org/wiki/Runtime_system)*
  
  **Stack:** "The JavaScript interpreter in a browser is implemented as a single thread... only 1 thing can ever happen at one time in the browser, with other actions or events being queued in what is called the Execution Stack. *[What is the Execution Context & Stack in JavaScript?](http://davidshariff.com/blog/what-is-the-execution-context-in-javascript/)*
  
  "A data structure which records...where in the program we are." *[-Philip Roberts, youtube](https://www.youtube.com/watch?v=8aGhZQkoFbQ)*
  
  **Stack Trace:** State of the stack at a certain point in time during the execution of the program.
  
  **Heap:** Basically where memory allocation happens.
  
  **V8 Runtime:** "...an open-source JavaScript engine developed by The Chromium Project for Google Chrome and Chromium web browsers." *-[Chrome V8 - Wikipedia](https://en.wikipedia.org/wiki/Chrome_V8)*
    
  **Blocking:** What happens when things are slow.  *[-Philip Roberts, youtube](https://www.youtube.com/watch?v=8aGhZQkoFbQ)*
  
  >There's almost no blocking functions in the browser... they're all make asynchronous.
    
   *[-Philip Roberts, youtube](https://www.youtube.com/watch?v=8aGhZQkoFbQ)*
    
  **Shim:**
  >A shim is a piece of code used to correct the behavior of code that already exists, usually by adding new API that works around the problem.
  
  *[Shim, MDN web docs](https://developer.mozilla.org/en-US/docs/Glossary/Shim)*
    
  ## One Thing At A Time, Not Really?
  
  The Javascipt runtime can only do one thing at a time. **But we *can* do things concurrently** because the browser is *more* than just the runtime. The event loop, callback queue, and WebAPI's act like threads.

  ## Event Loop
  
  If an asynchronous function is pushed on to the stack, the browser starts the async function. Once it's passed to the browser, javascript is done dealing with the async function so it's popped off the stack. Javascript continues dealing with the other code while the browser deals with the asynchronous function. Once the browser is done with the WebAPI it pushes the callback onto the task/callback queue. The event loop sees if the stack is empty. If yes, then the event loop takes the first callback in the callback que and passes it on to the stack.
  
  Because of this, `setTimeout()` is not a *guaranteed* time of executions, it's *minimum* time of execution.
  
  
  ## Callbacks Definition Confusion
  
  I'd been confused about callbacks because poeple use the term differently.
  
  The two ways to use the term ***callback***:
  
   - Any function that another function calls
   
   - More explicitely, an asynchronous callback, as in one that's going to be pushed into the callback queue in the future
  
  ## Render Queue
  
  The browser wants to repaint the view 60 frames a second. If there's nothing in the stack, it can. But, just like the callback queue, the render queue has to wait until the stack is clear. The render queue takes precedence over the callback queue. This is one reason it's good to use asynchronous code when possible. This way the render queue has more chances to update the view. 
  
  
- ## Thoughts and Feelings:

   I learned really slowly today. A 27 minute video over 1 hour and 45 minutes. I made sure to soak it all up! Sometimes learning slow is helpful to me. Sometimes I like just a fast review. 
   
   This video clarified up so much for me. I understand a lot mor about the under workings of javascript now.
   
   Even though I started my code organized, it got a little crazy again. It's hard to manage when it's not organized. I wonder how I can learn to organize it better? What can I search? what resources can I find?
   
   Studied while on the road from NOLA to Lafayette.
   
   ![screenshot](log_imgs/travel_3-29.gif)


## Day 87
### 3/28/19
- ## **100Daysofcode Tweet Search Project**

  ## Fixed Repeated Screen Names
  
  I forgot to apply my `uniqueInArray()` function to the `peopleLeft` array. That's why my generated tweet from yesterday was returning repeated screen names. So I added that in: 
  ```javascript
  peopleLeft = uniqueInArray(peopleLeft);
  ```
  
  ## Prototype
  
  I wanted to use prototypes so I thought I'd review them. I checked out a page on [w3schools](https://www.w3schools.com/jsref/jsref_prototype_string.asp) which defined prototypes:
  
  >The prototype property allows you to add new properties and methods to existing object types.
   
   from [*Javascript String Prototypes, w3schools*](https://www.w3schools.com/jsref/jsref_prototype_string.asp)
   
   W3schools has another page about [object prototypes](https://www.w3schools.com/js/js_object_prototypes.asp) but I think it's basically the same thing.
   
   It helps me to understand prototypes when I realize that:
   
   >All JavaScript objects inherit properties and methods from a prototype.
   
   from [*Javascript Object Prototypes, w3schools*](https://www.w3schools.com/js/js_object_prototypes.asp)
   
   I thought the page [*Javascript Object Prototypes, w3schools*](https://www.w3schools.com/js/js_object_prototypes.asp) did a good job of explaining protoypes.
   
   ## Prototypes on Native Objects- NO!
   
   I wanted to add a prototype to the String constructor, however I read: 
   
   >Only modify your own prototypes. Never modify the prototypes of standard JavaScript objects.
   
   from [*Javascript String Prototypes, w3schools*](https://www.w3schools.com/jsref/jsref_prototype_string.asp)
   
   More on why in [this stackoverflow thread](https://stackoverflow.com/questions/6223449/why-is-it-frowned-upon-to-modify-javascript-objects-prototypes). It has to do with namespaces.

   I wanted to add a prototype to string, because sometimes I like the look of dot notation better than passing in a string to a function ex: `myString.changeString()` vs `changeString(mystring)`. But maybe if I make more classes in my code I can add a prototype to what I want.
   
   I'm wondering **why you would ever want to add a prototype to your own objects?** Since you can create those objects from the start and just add the method there, why not just do that? 
   
   ## Promises
   
   I really need to undrestand promises because I'm using them in my app.
   
   I watched this [freecodecamp video on promises](https://www.youtube.com/watch?v=IGYxfTTpoFg). I really liked how the second part of that video shows the order that the different parts of the function are reached. This helps me see which part is asynchronous and which part goes in order.
   
   The instructor uses `setTimeout()` as the async function. But how would you get data from a json file using `new Promise()`? What does the async function look like for that? I used fetch before, but fetch is a totally different way of making a promise than the syntax for `new Promise()`. **How would you translate fetch into `new Promise()`?**
   
   I didn't finish reading this yet, but this article about [promises on hackernoon](https://hackernoon.com/understanding-promises-in-javascript-13d99df067c1) looks good and I think I'll check it out later.
   
   ## Async Functions
   
   This made me wonder what exactly is an async function? I know`setTimeout()` and `setInterval()` are async functions. Note: Today, I learned that:
   
   >These methods are not a part of JavaScript specification. But most environments have the internal scheduler and provide these methods. In particular, they are supported in all browsers and Node.JS.
   
   from [*Scheduling: setTimeout and setInterval, javascript.info*](https://javascript.info/settimeout-setinterval)
   
   I looked at the Mozilla Documentation on the [AsyncFunction object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/AsyncFunction) and [async function declarations](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function)
  
   >An asynchronous function is a function which operates asynchronously via the event loop, using an implicit Promise to return its result.
   
   from [*Async Function Declaration*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function)
   
   >The AsyncFunction constructor creates a new async function object. In JavaScript every asynchronous function is actually an AsyncFunction object.
   
   from [*AsyncFunction Object*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/AsyncFunction)
   
   I don't think I know what the event loop is. So I need to look into that if I want to understand asynchronous functions.
   
   ## JavaScript is Single Threaded
   
   *JavaScript is Single Threaded*: I've heard this before, but [this page](https://medium.com/codebuddies/getting-to-know-asynchronous-javascript-callbacks-promises-and-async-await-17e0673281ee) finally explained this idea well by comparing javascript to other langauges that are multi threaded: 
   
   >In programming languages like e.g Java or C# the “main program flow” happens on the main thread or process and “the occurrence of events independently of the main program flow” is the spawning of new threads or processes that runs code in parallel to the “main program flow”.
   
   >This is not the case with JavaScript. That is because a JavaScript program is single threaded and all code is executed in a sequence, not in parallel. In JavaScript this is handled by using what is called an “asynchronous non-blocking I/O model”. ***What that means is that while the execution of JavaScript is blocking, I/O operations are not.*** 
   
   [*more here*](https://medium.com/codebuddies/getting-to-know-asynchronous-javascript-callbacks-promises-and-async-await-17e0673281ee#b97e)
   
   ## I/O Operations
   
   **What is an I/O operation?**:
   
   >I/O operations can be fetching data over the internet with Ajax or over WebSocket connections, querying data from a database such as MongoDB or accessing the filesystem with the NodeJs “fs” module. All these kind of operations are done in parallel to the execution of your code and it is not JavaScript that does these operations; to put it simply, the underlying engine does it.
   
   [Getting to know asynchronous JavaScript: Callbacks, Promises and Async/Await](https://medium.com/codebuddies/getting-to-know-asynchronous-javascript-callbacks-promises-and-async-await-17e0673281ee#b97e)
   
   **So if I/O operations are separate from javascript where do they come from?**:
   
   >All these kind of operations are done in parallel to the execution of your code and it is not JavaScript that does these operations; to put it simply, the underlying engine does it.
   
   [Getting to know asynchronous JavaScript: Callbacks, Promises and Async/Await](https://medium.com/codebuddies/getting-to-know-asynchronous-javascript-callbacks-promises-and-async-await-17e0673281ee#b97e)
   
   
  ## To do
  
  I spent a lot of time learning instead of coding. I'm really glad I got to learn a bit more. I've been trying to figure out promises and prototypes for a while, but kept putting it off. I'm glad I started, I still have more to review.
  
  Because I spent a lot of time learning, I really didn't code much so I still have a lot to do on my app.
   
   - design
   - understand promises more
   - test going back and forth
     - back doesn't work correctly
   - tweet length validation
   - save user startDate and starting participants to local storage, skip some views if those are saved
   
- ## Thoughts and Feelings:

   I liked focusing on learning today, and taking a break from coding. I've *really* wanted to learn more about async and promises. So I was excited to look into it. I still have a lot to learn, I'll probably revisiti it tomorrow. I might read more in my free time.

  My hands on coding fuels my desire to learn. The more I code the more I feel I need to understand a new concept. 

## Day 86
### 3/27/19
- ## **100Daysofcode Tweet Search Project**

   My app now works! It still needs to be designed better, and it needs some tweeks here and there but it works for the most part.
   
   ![screenshot](log_imgs/tweet_3-27-19.gif)
   
   ## Form Validation
   
   The **next** button is not actually part of the form. I wonder if it should be so it could be more like submit and work with enter?
   
   I'm trying to think of the best way to validate the form.
  
   [Here on w3schools](https://www.w3schools.com/js/js_validation.asp) they make a function called `validateForm()` using the `onsubmit` event handle attribute.
   
   ```html
   <form name="myForm" action="/action_page.php" onsubmit="return validateForm()" method="post">
   ```
   
   I could create a `validateForm()` function for each view, or it could be one function that has a switch statement to deal with validations for each view. We only have to validate for 3 views: view1, view3, view5- all the views that have **input layouts**. My input layouts are created by calling `inputLayout()` so maybe we can somehow add the validation to this? I'm just brain storming here.
   
   ### Solution
   I decided to use a switch statement in a `validateForm()` function that is called in the `nextHandler()`.
   
   ```javascript
      const nextHandler=()=>{
         //check if 
         if (!validateForm()) return;
         nextView();
      }

      const validateForm = ()=>{
         let input = document.querySelector("input");
         if (input==null) return true;
         input = input.value;
         let valid=true;
         
         switch (currentView) {
           case 1:
               if(input.match(/[12]\d{3}-(0[1-9]|1[0-2])-(0[1-9]|[12]\d|3[01])/) == null){
               alert("The date you entered does not match the format YYYY-MM-DD.");
               valid = false;
               }
               break;
           case (3 || 5):
               if(input==""){
                   alert("Please plaste in the array that was auto-copied to your clipboard.");
                   valid = false;
               }
               break;
           default:
           valid = "true";
               break;
         }    
         return valid;
   }
   ```
   
   ### Date Validation Regex
   
   I found a regex pattern for the date format yyyy-mm-dd [here](https://www.regextester.com/96683 ):
   
   `([12]\d{3}-(0[1-9]|1[0-2])-(0[1-9]|[12]\d|3[01]))`
   
   I used `.match`, [more info here](https://www.w3schools.com/jsref/jsref_match.asp), to see if the user's input matches the regular expression.
   
   ## 280 Tweet Limit
   
   I need to make sure the generated tweet is within the 280 character limit required by twitter.
   
   Since the amount of mentions will differ, sometimes the tweet may be too long. So I need to edit my `tweetURL()` to dynamically create a tweet where the tweet text will be shorter if the mentions are too long when combined with the tweet text. 
     
     I also have to consider the possibility that the mentions themselves will be over 280 characters. In that case I might make several tweet links to congratulate users in groups.
     
     This all complicates my code. I need to go to the drawing board tomorrow and figure out how to handle this. `tweetURL()` is going to become big and clunky if I don't organize this.
     
   ## Renaming My Repository
   
   I had a typo in my repository so I renamed it following [this tutorial](https://help.github.com/en/articles/renaming-a-repository).
     
     
   ## To do
   
   - design
   - understand promises
   - test going back and forth
     - back doesn't work correctly
   - tweet length validation
   - save user startDate and starting participants to local storage, skip some views if those are saved
   
- ## Thoughts and Feelings:

   Back hurts a bit. There's so many little things I need to work on.
 
   
**Link to Work:** [MVC twitter participant project](https://github.com/dangerousdashie/100daysofcode_post_search/tree/fb451f6c0daacd1e0280ac526300ec40a730cf23/MVC%20app)

## Day 85
### 3/26/19
- ## **100Daysofcode Tweet Search Project**

   I followed up with someone who wanted help from yesterday. I didn't get enough of my own coding done because of this. I should keep my volunteer hours within certain times and not agree to more later. 
   

   ## Twitter share link
   
   I found that this prefix: ***`http://twitter.com/share?text=`*** did not work well. I believe it's because you need to include a url or it acts up. It generated a tweet that inserted the url for the tweet back into the tweet. I guess share expects to share a link and without one it just uses itself as the link.
   
   The bolded section of the link was put there automatically some how.
   
   `https://twitter.com/intent/tweet?text=test%20test%20%3A%20%40test&`**url=https%3A%2F%2Ftwitter.com%2Fshare%3Ftext%3Dtest%2520test%2520%3A%2520%40test&original_referer=https%3A%2F%2Ftwitter.com%2Fshare%3Ftext%3Dtest%2520test%2520%3A%2520%40test`***
   
   ![screenshot](log_imgs/tweet_3-26.png)
   
   However, ***this prefix worked*** without a link: `https://twitter.com/home?status=`
   
- ## Thoughts and Feelings:
   
   I feel a little burnt from extending my volunteer hours.
   
   The reason I agreed to follow up was because it seemed like this coder was the only person that was asking me for help yesterday. But then a bunch of people asked me for help after them. I didn't expect that many people.
   
   Some days no one asks for help, other days too many people do. I think it has to do with who retweets my Open DM tweets.
   
   I need to respect my time so I don't burn out. Back to coding for myself tomorrow.

## Day 84
### 3/25/19
- ## **100Daysofcode Tweet Search Project**

   Today I spent a lot of the day helping others on twitter DM, so my coding time was limited.

   ## Preview Github HTML Files
   
   This has been a helpful tool for me. You can go to [htmlpreview.github.io](http://htmlpreview.github.io/) and paste any github html link there inorder to actually see how the html renders. I find that for some files it doesn't work and I'm not sure why.
   
   You can also prepend to the URL **http://htmlpreview.github.io/?**
   
   *Example:*
   
   [`http://htmlpreview.github.io?`https://github.com/dangerousdashie/Simple-Button-Click-Counter/blob/master/index.html](http://htmlpreview.github.io?https://github.com/dangerousdashie/Simple-Button-Click-Counter/blob/master/index.html)
   
   ## Twitter Tweet Link
   
   I worked on creating a link that auto tweets for the user to congratulate their fellow participants for continuing with the challenge. 
   
   **This [stackoverflow thread](https://stackoverflow.com/questions/6208363/sharing-a-url-with-a-query-string-on-twitter) gives two ways to create an auto-tweet link:**
   
   - `http://twitter.com/share?text=`**text goes here**`&url=`**http://url goes here**`&hashtags=`**hashtag1,hashtag2,hashtag3**
   
     - Example: [`http://twitter.com/share?text=Im Sharing on Twitter&url=https://stackoverflow.com/users/2943186/youssef-subehi&hashtags=stackoverflow,example,youssefusf`](https://twitter.com/intent/tweet?url=https%3A%2F%2Fstackoverflow.com%2Fusers%2F2943186%2Fyoussef-subehi&hashtags=stackoverflow%2Cexample%2Cyoussefusf&text=Im%20Sharing%20on%20Twitter&original_referer=https%3A%2F%2Ftwitter.com%2Fshare%3Ftext%3DIm%2520Sharing%2520on%2520Twitter%26url%3Dhttps%3A%2F%2Fstackoverflow.com%2Fusers%2F2943186%2Fyoussef-subehi%26hashtags%3Dstackoverflow%2Cexample%2Cyoussefusf)
   
   - Intents
   
     - Intent [Web Intents](https://developer.twitter.com/en/docs/twitter-for-websites/tweet-button/overview)
   
     - Tweet Button: [Tweet Button](https://developer.twitter.com/en/docs/twitter-for-websites/tweet-button/overview)
     
- ## Thoughts and Feelings:
   
   Karma is abound.

   I was distracted today because our tire on our RV (where we live full time) blew out. My sweet beautiful amazing spouse took care of calling AAA it so I could continue to code. Thanks to him not only did I get to code, but I also was able to help others. Because of him so many coders benefited.
   
   I opened my DM up to help people, today. I was able to help a lot and for those that I couldn't help I referred them to other places for help. Someone even offered to help me, I couldn't take them up on it because it was towards the end of my coding time. But I might send them my code tomorrow.
    
   We were very lucky because the AAA guy said the tire could have exploded while driving. He was very generous and patient. He could not jack up our RV high enough but a stranger who lived nearby brought him a bunch of wood to help jack the tire up. The AAA guy said most AAA peolpe would just say fuck it and tow us to a tire shop. This guy went above and beyond.
   
   What an amazing day!
   
   
     
   

## Day 83
### 3/24/19
- ## **100Daysofcode Tweet Search Project**

   ## Daylight Savings
   
   I haven't look at these yet, but in response to my trouble with daylight savings yesterday [Brandon Kirkland on twitter](https://twitter.com/BrandoKirkland) suggested these resources:
   
   - [Youtube: The Problem with Time & Timezones - Computerphile ](https://www.youtube.com/watch?v=-5wpm-gesOY&feature=youtu.be)
   - [Stackoverflow: Daylight saving time and time zone best practices ](https://t.co/7hXTOHlDkn)
   
   ## AJAX
   
   I'm going to spend a lot of time today on AJAX. 
   
   I found this page, [Beginners Guide To Fetching Data With (AJAX, Fetch API & Async/Await)](https://dev.to/bjhaid_93/beginners-guide-to-fetching-data-with-ajax-fetch-api--asyncawait-3m1l), which shows addtional ways to get data.
   
   The author lists three ways:
   - AJAX
   - Fetch API
   - Async/Await
   
   I believe the author's terminology is  a little confusing here, because I think all of these are considered Ajax. But I think AJAX refers to the original Ajax. AJAX vs. Ajax. 
   
   > We now use “Ajax” as a generic term for any client-side process which fetches data from a server and updates the DOM dynamically without a full-page refresh. Ajax is a core technique for most web applications and Single-Page Apps (SPAs).
   
   from [*sitepoint.com*](https://www.sitepoint.com/xmlhttprequest-vs-the-fetch-api-whats-best-for-ajax-in-2019/)
   
   The author means making `XMLHttpRequest` object when he says's AJAX. Making an `XMLHttpRequest` object was the original way to use Ajax.
   
   ## Fetch VS XMLHttpRequest
   
   For some reason I'm able to get the response text in another part of my code if I use fetch, but not if I make an XHR object.
   
   ### XHR Object
   
   #### Undefined `instructionsText`
   
   ```javascript
   
   //-------------------------------data is NOT DEFINED in this function
   const view1 = ()=>{
      inputLayout("small");
      const instrEl = document.querySelector(".instructions");
      instrEl.innerHTML = instructionsText.view1[0]; //instructionsText is undefined 
   }
   
   //-----------------------AJAX with XHR object
   
   let instructionsText  = null;
   
   let xhr = new XMLHttpRequest;
   //Call the open function, GET-type of request, url, true-asynchronous
   xhr.open('GET', 'https://api.github.com/users', true)
   //call the onload 
   xhr.onload = function(){
      //check if the status is 200(means everything is okay)
      if (this.status === 200){
         //return server response as an object with JSON.parse
         console.log(JSON.parse(this.responseText));
      }
   }
   //call send
   xhr.send();
   ```
   
   ### Fetch
   #### Defined `instructionsText`

   ```javascript
   //-------------------------------data IS DEFINED in this function 
   const view1 = ()=>{
      inputLayout("small");
      const instrEl = document.querySelector(".instructions");
      instrEl.innerHTML = instructionsText.view1[0]; //instructionsText is defined 
   }
   
   //------------------------AJAX with Fetch
   
   let instructionsText  = null;
   
   fetch('instructions.json')
      .then(resp => resp.json())
      .then(obj => instructionsText = obj)
   ```
   
   ### Why is this happening?
   
   I'm wondering if the reason we get different results is because `fetch` and `new XMLHttpRequest` return different objects. `fetch` returns a Promise. `new XMLHttpRequest` ofcourse gives us an `XMLHttpRequest`. I don't understand the difference yet.
   
   This page looks like it might be helpful in understanding what's going on: [Getting to know asynchronous JavaScript: Callbacks, Promises and Async/Await](https://medium.com/codebuddies/getting-to-know-asynchronous-javascript-callbacks-promises-and-async-await-17e0673281ee). I didn't read the whole thing yet.
   
   I'm still not sure why `fetch` works the way I want it to but `new XMLHttpRequest` doesn't. I'm going to move on. Maybe when I start playing around with Ajax more I'll understand more.
   
   ### Update
   
   Fetch also doesn't work if I load `view1` right away by manually calling the `startHandler()` that brings up the view. Normally, the `startHandler` is triggered by a click event when the user clicks "next". Perhaps that gives the code enough time to get the data. 
   
  ## To Do
  
  - change next click event to a submit button and a submit event so pressing enter works
  - finish view 6
  - layout

- ## Thoughts and Feelings:

   Today I used a computer burqa/chuppah/sukkah to protect my eyes from eye strain. This helped a lot. But I need a better solution because the fabric falls into view. Perhaps if I can get something to tent it up.
   
   ![screenshot](log_imgs/burqa_3-24.gif)
   
**Link to Work:** [MVC twitter participant project](https://github.com/dangerousdashie/100daysodcode_post_search/tree/3dbaaec3ddd25639b86d6561c9335507d2f87fdd/MVC%20app)
   

## Day 82
### 3/23/19
- ## **100Daysofcode Tweet Search Project**

   ## Daylight Savings Problems
   
   The `OneHundredCodeDays` class method, `.numberOf(date)`, is giving me the wrong day. It's giving me a day less than what it should be.
   
   ```javascript
    numberOf(date){ 
       return Math.floor((date-this.startDate)/86400000) + 1;
    }
    ```
   
   I tried settings the time of the date passed into the function to the same time as the `.startDate` so that the days would subtract to whole days. I added this before the `return` statement.
   
   ```javascript
       date.setHours(oneHundred.startDate.getHours(), oneHundred.startDate.getMinutes(), oneHundred.startDate.getSeconds(), oneHundred.startDate.getMilliseconds());
   ```
   
   But it still wasn't working. 
   
   I noticed that some dates worked correctly with the `.numberOf()` method but some didn't. I manually tested `.numberOf()` with a bunch of dates. I found that `.numberOf()` gives the correct day, until daylight savings happens on March 10th.
   
   ![screenshot](log_imgs/daylightsavings_3-23.png)
   
   So the problem has to do with daylight savings. 
   
   Daylight savings day is an hour less. The 23 hour day throws our entire calculation off by one hour. When we floor down this calculation, we floor down **23 full hours**, when we should round up **one hour**.
   
   So I changed `.floor()` to `.round()`. Depending on whether we're going in or out of daylight savings, the code `(date-this.startDate)/86400000` will only leave us a remainder of time that is an *hour more* or an *hour less*. No more, no less. So rounding will work to get us to the correct date, instead of flooring.
   
   
   ## AJAX
   
   I forgot that I'm already running a live server through the live server extension for Visual Studio Code. So I didn't need to to anything extra to set one up.
   
   Using AJAX was more confusing than I thought it would be. I used it before with jQuery. Now I'm doing it with vanilla JavaScript and I'm using the data during different points of the code.
   
   ### Getting the JSON file
   
   I used this tutorial from [gomakethings.com](https://gomakethings.com/ajax-and-apis-with-vanilla-javascript/) to get my data.
   
   ```javascript
   let xhr = new XMLHttpRequest();

   xhr.onload = function(){
      if(xhr.status >= 200 && xhr.status < 300){
           console.log("success", xhr);
      }else{
           console.log('The request failed!');
      }
      console.log('this always runs');
   }
   xhr.open('GET', 'instructions.json');
   xhr.send();
   ```
   
   Then I tried to get the data by adding the code below on the next line after `xhr.send()`. You have to use JSON.parse(), because the response is just going to return a string. We want it to be a JSON object so we can use dot notation and bracket notation to easilty traverse throught the data:
   
   ```javascript
   const instructionText = JSON.parse(xhr.response);//doesn't work!
   ```
   
   This doesn't work because, even though it's after `xhr.send()`, `xhr.send()` is asynchronous. So `.send()` doesn't actually finish running before the next line. So we havent actually gotten back our XHR response yet.
   
   In order to get the data, we have to innitialize `instructionText` within the success clause body. I'm not sure if 'success clause body' is the correct way to say it, but this is what I mean:
   
   ```javascript
   let instructionText; //declaration
   
   let xhr = new XMLHttpRequest();

   xhr.onload = function(){
    if(xhr.status >= 200 && xhr.status < 300){
        console.log("success", xhr);
        instructionText = JSON.parse(xhr.response); //-----------initialization----------
   }else{
        console.log('The request failed!');
   }
    console.log('this always runs');
   }
   xhr.open('GET', 'instructions.json');
   xhr.send();
   ```
   
   ### Using The Data From The AJAX Request
   
   Now if we use `instructionText` within our app, we'll still have a problem.
   
   ```javascript
   const view1 = ()=>{
      inputLayout(instructionsText.view1[0], "small"); //won't work, instructionsText is undefined
   }
   ```
   
   Trying to retrieve the `instructionText` in our `view1()` function doesn't work, again because we're accessing `instructionsTest` in a function that can be reached before the XHR request returned a response.
   
   So what should I do to get the response text where it needs to be?
   
   In this tutorial on [code-maven.com](https://code-maven.com/ajax-request-for-json-data), they create a function that takes a url for the JSON and callback function. The callback function they pass into their ajax_get() function, takes the data and puts it where it needs to be. 
   
   I'm wondering how I could implement this for my app. The problem is, since my app is a single page application, I can't place the data all at once. The code on [code-maven.com](https://code-maven.com/ajax-request-for-json-data) places all the data on the page as soon at the XHR request succeeds. But I need to place different data within the response at different times depending on what view the user is on.
   
   This is where I left off today.
   
   
- ## Thoughts and Feelings:

   I coded while in the passengers seat and the sun caused eye strain. Next time I can code under a towel or in the back.
   
**Link to Work:** [MVC twitter participant project](https://github.com/dangerousdashie/100daysodcode_post_search/tree/3b8ef1e61eba8b49d32c508c665fe1434cc29f48/MVC%20app)

   

## Day 81
### 3/22/19
- ## **100Daysofcode Tweet Search Project**
   
   ## Updating `Search` Class
   
   I updated `Search` so that it can create a string for multiple day numbers. This way I can see who posted for *both* **Day 1** and **Day 2** in the same query, for example, instead of only searching for **Day 1** or any other day by itself.
   
   This is useful because some people haven't done **#100DaysOfCode** consecutively. Participants can skip 1 day every 2 weeks according to **#100DaysOfCode**. So participants who started when I did, might not be up to day 81 today.
   
   That works out perfectly because that means you can be up to **7 days behind** on **#100DaysOfCode**. Twitter limits my string search after I add an **8th day string query**. So 7 works out.
   

   ## Range Helper Function
   
   There isn't a range function in javascript. Maybe there is or will be in a new syntax. 
   
   I made a range helper function.
 
   
    ```javascript
   const range = (start, end)=>[...Array(end-start+1).keys()].map(x=>x+start);
   ```
   
   If we call `range(3,6)` we would get `[3,4,5,6]`.
   
   This function uses the `Object.keys()` method.
   
   >The Object.keys() method returns an array of a given object's own property names, in the same order as we get with a normal loop.
   
   from [*Mozilla Documentation*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/keys)
   
     I needed to create a range helper function so I could pass an array with a range of numbers to the updated `Search` class. With my range helper function I can pass this code into the `Search` method:
   
   ```javascript
   num: range(oneHundred.numberOf(weekAgo), oneHundred.numberOf(yesterday)
   ```
   
   ## Other Things Done and To Do
   
   - Done
     - Added JSON.parse and .stringify when copying the array of screennames to the clipboard. I thought this was cleaner than what I had before.
    
   - To Do
     - AJAX to get JSON
     - design
    
- ## Thoughts and Feelings:

   I got to cut this short because I'm running low on power. Hope there's not a lot of typos. Coded in the driving rialta today. I think I'm liking meditating.
   
**Link to Work:** [MVC twitter participant project](https://github.com/dangerousdashie/100daysodcode_post_search/tree/6a6709cadc3f9c09001aef718281a37422ef850c/MVC%20app)
   

## Day 80
### 3/21/19
- ## **100Daysofcode Tweet Search Project**

   ## JSON

   I made a JSON file that holds all the text for the instructions. This is more organized that keeping the text in the controller. 

   I thought if a JSON file is local, you could get it without setting up a server, but I think you still have to. Unless the file is formatted as a javascript file. But that's not the right way to do it. It should be a JSON file and it should be through a server.
   
   More info in this [stackoverflow thread](https://stackoverflow.com/questions/19706046/how-to-read-an-external-local-json-file-in-javascript).
   
   I didn't get the data yet. I need to review how to do that with AJAX. I've done it before and it shouldn't be hard.
   
   ## `:not()`
   
   I used `:not()` to filter out the promoted tweets when getting a list of screennames that tweeted about #100daysofcode. You'll get random promoted tweets like from Ritz Crackers or whatever. So you can filter them because they have a class called .promoted-tweet:
   
   ```javascript
   document.querySelectorAll(".tweet.js-stream-tweet:not(.promoted-tweet)")
   ```
   
   >The :not() CSS pseudo-class represents elements that do not match a list of selectors. Since it prevents specific items from being selected, it is known as the negation pseudo-class.
   
   from [*Mozilla Documentation*](https://developer.mozilla.org/en-US/docs/Web/CSS/:not)
   
   ## Removing Duplicates in an Array with filter()
   
   Just incase someone posted twice on the same day, I have to remove the duplicate screennames. I made a helper function to do this.   
   
   ```javascript
   const uniqueInArray = (arr)=>(arr.filter((x,i)=> arr.indexOf(x)>=i));
   ```
   
   At first I couldn't figure out the best way write this function. I knew I could use `.filter()`. I got the idea to use `indexOf` from [gomakethings.com](https://gomakethings.com/removing-duplicates-from-an-array-with-vanilla-javascript/#using-the-array-filter-method). 
   
   ## Organizing Views
   
   I created an array that holds the function names that control each view:
   
   ```javascript
   let view = [view0, view1, view2, view3, view4];
   ```
   
   Now, I can just go back and forth through the array to call the next and last view. What I had before was messier. It involved changing which function was next at every view. This is easier to change and read. 
   
   ## DevTools Step Over Vs. Step Into
   
   I finally understand the difference between *step over function* and *step into function* in the debugger. Sometimes they do the same thing which is why it confused me at first.
   
   I learned that if a line has a function call in it:
   
   - *Step over* will skip to the next line in the *current function*. 
   
   - *Step into* will take you to the function that was *just called*.
   
   ## Do To
   
   - Add the day number string to the first url
   - last views
   - design
   
   
- ## Thoughts and Feelings:

   Organizing my code paid off. Since I had so many reasable and well organized functions and classes, I was able to code the next views so fast. 
   
   Instead of creating all sorts of components and layouts for the new views, I just called the layout functions that I made. I passed the relavent arguments. It was so fast and easy.
   
**Link to Work:** [MVC twitter participant project](https://github.com/dangerousdashie/100daysodcode_post_search/tree/28c82f643acff0f79b55ef377c5029e07e0c082e/MVC%20app)

## Day 79
### 3/20/19
- ## **100Daysofcode Tweet Search Project**
   
   ## Rest Parameters
   If you use rest parameters, is it *optional* to pass any arguments into the function? It seems like it from my code. 
   
   Now I'm wondering if regular parameters are sometimes optional too. Like is it optional to pass them if they're not actually 100% necessary in the function? I'll have to play around with this.
   
   ## Syntax Requirements for Statements in Arrow Functions
   
   I tried to use a statement in an arrow function using parenthesis:
   
   ```javascript
   mainEl.childNodes.forEach((x)=>(if (!except.includes(x)) mainEl.removeChild(x)); //syntax error
   ```
   
   This gave me a syntax error because an if statement is a **statement** not an **expression**. 
   
   ***Statements must be put in braces {} in arrow functions.***
   
   >If an expression is the body of an arrow function, you don’t need braces:

   ```javascript
   asyncFunc.then(x => console.log(x));
   ```
   >However, statements have to be put in braces:
   
    ```javascript
   asyncFunc.catch(x => { throw x });
   ```
   from [exploringjs.com](http://exploringjs.com/es6/ch_arrow-functions.html)
   
   ### What's the difference between statements and expressions?
   
   >Expressions produce (are evaluated to) values. Examples:
     ```javascript
   3 + 4
   foo(7)
   'abc'.length
   ```
   >Statements do things. Examples:
   
    ```javascript
   while (true) { ··· }
   return 123;
   ```
   >Most expressions can be used as statements, simply by mentioning them in statement positions:
   
   ```javascript
   function bar() {
    3 + 4;
    foo(7);
    'abc'.length;
   }
   ```
   from [exploringjs.com](http://exploringjs.com/es6/ch_arrow-functions.html)
   
   ## Removing Elements With `forEach()` Doesn't Work Well
   
   ```javascript
   mainEl.childNodes.forEach((x)=>{
       mainEl.removeChild(x);
   });
   ```
   The above **leaves** child nodes. I expected all child nodes to be cleared, but they aren't.
   
   I think it's because inorder to find element x, `forEach()` is simply iterating through the indices of `mainEl.childNodes`. So first it takes `mainEl.childNodes[0]`, then `mainEl.childNodes[1]`, then index 2, etc:
   
   Value of x:
   
   `mainEl.childNodes[0]`
      
   `mainEl.childNodes[1]`
   
   `mainEl.childNodes[2]`
   
   `mainEl.childNodes[3]`
   
   `mainEl.childNodes[4]` ... etc....
   
   But since we are *removing nodes*, we are *messing with the indices.*
   
   
   At first these indices map to these elements:
   
   ```html
   <div id="main">
      <div id="one"></div> 
      <div id="two"></div> 
      <div id="three"></div> 
      <div id="four"></div> 
   <div>
   ```
   ```javascript
   mainEl.childNodes[0]  <div id="one"></div>
      
   mainEl.childNodes[1]  <div id="two"></div>   
   
   mainEl.childNodes[2]  <div id="three"></div> 
   
   mainEl.childNodes[3]  <div id="four"></div> 
   ```
   
   But once we remove the first child node, `mainEl.childNodes[0] //<div id="one"></div>`, we are left with a new NodeList.
   
   Now the element with id `#two` is in index 0.
   
   ```javascript
   mainEl.childNodes[0]  <div id="two"></div>   //[0] used to be #one
   
   mainEl.childNodes[1]  <div id="three"></div> 
   
   mainEl.childNodes[2]  <div id="four"></div> 
   ```
   
   But `forEach()` already dealt with index 0, so it moves on to *index 1* which has now changed from the element with id `#two` to `#three`. `forEach()` skips element `#two`. 
   
   This pattern continues and we end up skipping a lot of elements.
  
   ### This way works:
   
   ```javascript
   while (mainEl.hasChildNodes()){
       mainEl.childNodes[0].remove();
   }
   ```
   ## While Loop, Infinite Loop
   
   I ran into an infinite loop while working with a while loop. I always do this! A nice way to stop an infinite loop when you're developing and debugging is like this:
   
   ```javascript
   let loop = 0;
   
   while(somethingIsTrue){
      loop++
      //...some code here...
      if(loop>100){ 
         console.log("Over 100 loops, exiting function");
         return;
      }
   }
   ```
   
   ## `Document.execCommand('copy')` Not working in My Test
   
   I have this `copyToClipboard()` snippet which I got from [hackernoon.com](https://hackernoon.com/copying-text-to-clipboard-with-javascript-df4d4988697f). This snippet is originally from [30 Seconds of Code](https://github.com/30-seconds/30-seconds-of-code).
   
   The `copyToClipboard()` function wouldn't work while I was testing. That's because when I do unit tests, I am probably doing them wrong. I just call all the functions:
   
   ```javascript
   //------------------ test
   startHandler();
   document.querySelector(".input").value = "2019-01-01";
   nextHandler();
   ```
   
   Normally, `nextHandler()` is triggered by an event- a click on the next button. But here in my test we just call it. 
   
   `copyToClipboard()` is inside of `nextHandler()`.  `Document.execCommand('copy')`, which is  called inside `copyToClipboard()`, can only be executed as the result of a user action.
   
   Since my test calls `nextHandler()` without replicating a user click on the next button, this function doesn't work when testing. There was no user action.
   
   I wonder if it's possible to write a test that mimics a user action? I know that my tests are not really legit unit test. But it seems unsafe for any unit test to replicate a user action. Because then you could use unit tests in your actual code to do all sorts of unsafe things that browsers normally don't allow.
   
   ## Done & To Do
   ### Done
   My code now auto-copies the returned array of participants to the clipboard. So the user never has to select and copy.
   
   I cleaned up my code to make it more dry. I have a function, `clearLayout(...except)` that clears all children of the main div, except for nodes specified in the parameters.
   
   I created a new layout function, `linkLayout(url)`, which creates a layout with a link made from any url passed in to the function. We'll use this everytime we create a new twitter search link.
   
   ### To Do
   - View for inputing array of screennames
     - make sure screennames are not repeated
     - copy new code for comparing the screen names
   -design

- ## Thoughts and Feelings:
   
   Much more focused today! No podcasts distracting me.
   
**Link to Work:** [MVC twitter participant project](https://github.com/dangerousdashie/100daysodcode_post_search/tree/1a1a7fe65b645ab88a5a83f818438f49ce815f6a/MVC%20app)
   
 
## Day 78
### 3/19/19
- ## **100Daysofcode Tweet Search Project**
   
   Today I added functionality to the elements for the first and some of the second view. 
   
   My log won't be the best today, see **thoughts and feelings**.
   
   ## `replaceChild()
   
   I used `replaceChild()` to change some of the elements from one view to another. I don't know if this is the best way. But I got some practice with this function I never used before.
   
   ## Search Class Needs to be Revisted
   
   Now that I'm using the `Search` class, I see that the constructor should be set up differently. Because we might only want to set one day to search. And then later add an until date. So we'd want to set the until date later. I'm not totally sure. I need to think this through.
   
   ## Things To Do;
   
    - Make the code that gets autocopied to the clip board include `copyToClipBoard()` and copy the returned array so the user can easily paste it back into the program.
   
- ## **Thoughts and Feelings:**
   
   I'm a bit frazzeled because it's a travel day and I was distracted by the podcast playing in the background even over my white noise generator. To be honest it sounded so interesting that at points I tried to listen and code at the same time. 
     
   I turned my white noise off so I could listen. Not a great idea!
     
   I'm able to code if I leave my white noise on and focus. I've done it many times before. But I didn't turn my white noise up high enough so I coudl still make out some words and it sounded so interesting.
     
   So obviously I wan't super focused.
     
   I should note, compared to how much I used to struggle with coding while driving, I'm a million times better even on days I'm distracted. #100daysofcode helped me build a habit code no matter what. I've gotten much better at coding and blocking out distractions no matter where I am. 
      
  ![screenshot](log_imgs/travel_3-19.gif)
   
**Link to Work:** [MVC twitter participant project](https://github.com/dangerousdashie/100daysodcode_post_search/tree/5b3076ce1dfb55505c90be752e7fedc7d0bc00c7/MVC%20app)

## Day 77
### 3/18/19
- ## **100Daysofcode Tweet Search Project**

   I was so organized today. My code is so easy to see and understand and it's very dry and I think it performs well too. Here are the things I learned and did:
   
   ## CSS Responsive square
   I found a way to make a responsive square in css that keeps its perspective. I didn't use this today but I had been wondering about it. The trick is to use padding because:

   >...padding is calculated as a percentage of its parent element’s width, not height. This rule applies, even for 'padding-top' and 'padding-bottom', despite being vertical measurements.

   from [*spin.atomicobject.com*](https://spin.atomicobject.com/2015/07/14/css-responsive-square/)
   
   ## Responsive Pages required CSS
   
   I wanted to put this here, I always lose it. This code helps your page better work with responsive design. It changes the box-sizing on all elements. I forget what the max width for img does but it's explained somewhere in the Responsive Layout tutorial on lynda.
   ```css
   html {
        box-sizing: border-box;
    }
    img {
        max-width: 100%;
    }
    *,*:before,*:after {
        box-sizing: inherit;
    }
   ```
   ## Confused about: CSS inheritance & Document Fragments
   
   I'm confused about which things are automatically inherited in css. I guess you can find it out by seeing what the default value for each property is. I thought that `font-size` would be inherited but it didn't inherit when I tried to inherit it from `html, body` where I set it.

   I'm confused when exactly you'd use a document fragment. Is it when you need to add a bunch of elements? I think so because otherwise if you're looping through adding a bunch of elements I think it slows down performance.
   
   [Mozilla Docs for documents fragments.](https://developer.mozilla.org/en-US/docs/Web/API/Document/createDocumentFragment)
   
   ## App Progress
   
   I continued my app's MVC structure. I drew out what my views would be like and made notes for my model, controller, and views javascript files.
   
   ![screenshot](log_imgs/notes_3-18.jpg)
   
   This really helped me think through what was going to happen.
   
   ## MVC
   
   In my views file I have a function that makes elements, `elWithClass(el, ...className)`. You pass the class names and element types. I successfully used rest parameters for this function. Learning about rest parameter the other day turned out very helpful:
   
   ```javascript
   const elWithClass = (el, ...className)=>{
       const elem = document.createElement(el);
       elem.classList.add(...className);
       return elem;
   }
   ```
   
   Then there are other function in views for each element we need like a `start()` function that makes a start button. These functions call `elWithClass()`. The `input()` function bellow calls `elWithClass()`. It passes `input` as the element type, `input` as the class name, and then takes any other classes passed into it.
   
   ```javascript
   const input = (...classNames)=>(elWithClass("input", "input", ...classNames));
   ```
   
   My controller.js file controls which views get pulled up and when.
   
   I'm wondering if this is similar to how react.js works. I'm making all these reusable components. I think that's what react.js does.
   
   Of course, in models we have the classes we made before: OneHundredCodeDays, and Search. I don't know why I said we, I guess if you're reading this, you're a part of my journey.
   
   I ended with successfully getting a template up for the first view. Look how simple and beautiful!
   
   ![screenshot](log_imgs/view_3-18.gif)
   
   **Thoughts and Feelings:**
   
   Today was great. My mind was clear. My code was clean. Glorious. Maybe because I started meditating? Or maybe just because I'm thinking more about how to have clean, readable code.
   
**Link to Work:** [MVC twitter participant project](https://github.com/dangerousdashie/100daysodcode_post_search/tree/ce713d795e02452b152076581dca86bad0e609be/MVC%20app)
   
## Day 76
### 3/17/19

- ## **100Daysofcode Tweet Search Project**

   ## Do I know how to use a favicon?
   
   I thought I did but it's not loading. I need to revisit this.
   
   ## When do you use a class and when do you use a function?
   
   I'm trying to understand when to use a function and when a class. I think if I'm returning an object with a bunch of methods and properties, that should be a class. I read a little bit of [this thread on the subject](   https://www.codecademy.com/en/forum_questions/5149f1c5b216ffa8f200019e), but I should read more later.
   
   ## Classes: Getters and Setters
   
   I learned about getters and setters today.
   
   ### Getters
   
   >The get syntax binds an object property to a function that will be called when that property is looked up.
   
   ```javascript
   var obj = {
      log: ['a', 'b', 'c'],
      get latest() {
          if (this.log.length == 0) {
            return undefined;
          }
       return this.log[this.log.length - 1];
      }
   }
   console.log(obj.latest);
   ```
   from [Mozilla Documentation](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/get)
   
   So even though you can get the property like a property- `obj.latest`- it's really going to call the function `.latest()` and get what the function returns as the property.
   
   ### Setters
   > The set syntax binds an object property to a function to be called when there is an attempt to set that property.
   
   ```javascript
   var language = {
      set current(name) {
          this.log.push(name);
      },
      log: []
   }

   language.current = 'EN';
   language.current = 'FA';

   console.log(language.log);
   // expected output: Array ["EN", "FA"]
   ```
   from [Mozilla Documentation](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/set)
   
   ## Error handling in classes?
   
   I'm wondering if there's a proper way to I throw an error if the wrong parameters are passed to the constructor. I havn't done a lot with error handling. In my class I just do simple if statements (*see below*) but I don't think this is the proper way.
   
   ## Is my Search class constructor too busy?
   
   I changed my `searchString()` function to a class called `Search`.
   
   I spent a lot of time thinking about this class and classes in general.
   
   It doesn't look clean and neat like all the other classes I've seen. 
   
   Usually class constructors just set `this.someproperty = property;`. But since I need to do a lot to the inputs, my constructor has all these functions and conditional statements that make it hard to read. 
   
   Should some of these actually be in a getter? Is that all that getters are for, to clean the code up?
   
   
   ```javascript
     class Search {

      constructor (obj){
          //temporarily save the passed properties into these variables for easier read and make empty if no params
          let since = obj.since || "";
          let until = obj.until || "";
          let range = obj.range || "";
          let num = obj.num || "";
          let participants = obj.participants || "";

          //errors, how do we do this?
          if (range!="" && until != ""){ return console.error("error, you can't have a 'range' and an 'until' value. You must have one or the other")};
          if (since==""){ return console.error("error, you must have a since value")};


          //should this function be in the search class constructor or on it's own?
          const formatDate = (date)=>{ 
              const year = date.getFullYear();
              const month = date.getMonth() + 1;
              const day = date.getDay();
              return `${year}-${month}-${day}`;
          };

          //this gets the until date
          const untilDate = ()=>{
          //until using until or range value
              if(until != ""){
                  return until;

              } else if (range == "" && until == ""){
                  let day = new Date();
                  day.setDate(since.getDate()+1)
                  return day;

              } else if (range != ""){
                  let day = new Date();
                  day.setDate(since.getDate()+range);
                  return day;
              } 
          };

          //since
          this.since = `since:${formatDate(since)}`;
          //sets the until property
          this.until = `until: ${formatDate(untilDate())}`;

          //participants
          if (participants !== ""){
              this.participants = participants.reduce(((a,c,i)=>{
                  let x; 
                  i==1? x =`from:${a}, OR from:${c}` : x = `${a}, OR from:${c}`;
                  return x;
              }))
          };

          //query with day number
          if (num!=""){
              this.number = `(Day${num} OR “day ${num}” OR r1d${num} OR “r1 d${num}”)`;
          }
      }            

      //could we do the below with dot chaining:
      url(...args){
          const string = args.join(' ') + ` #100DaysOfCode`;
          const urlStart = "https://twitter.com/search?f=tweets&vertical=default&q="
          const url = encodeURI(string).replace(/[(]/g, '%28').replace(/[)]/g, '%29').replace(/[#]/g, '%23').replace(/[:]/g, '%3A')
          return urlStart + url;
      }
   ```
   ## Model View Controller
   
   I started to set up this project with a **model view controller** design pattern.
   
   ## To Do
   
   - I think I need to go back to the drawing board, aka my note pad and think through the MVC pattern and how it will apply to my app. 
   
   - I might want to reach out for help and advice on the organization of my `Search` class.
   
   **Thoughts and Feelings:** There were two flies annoying the bejezus out of me. They need to leave or die!
   
**Link to Work**: [100Daysofcode Tweet search](https://github.com/dangerousdashie/100daysodcode_post_search/tree/e2af718471d2e3f3de160ecc1bfb55eed74d0b41/MVC%20app)

## Day 75
### 3/16/19

- ## **100Daysofcode Tweet Search Project**

   ## `reduce()`
   
   I got reduce to work!
     
   ```javascript
   participants.reduce(((a,c,i)=>{
        let x; 
        i==1? x =`from:${a} OR from:${c}` : x = `${a} OR from:${c}`;
        return x;
   }))
   ```
   
   If participants here is **["DashBarkHuss","Dominus_Kelvin","mahakothuri"]**, then the output is: **"from: DashBarkHuss OR from:Dominus_Kelvin OR from: mahakothuri"**.
   
   ## ES6 Destructuring, doesn't work with functions?
      
   I tried to use ES6 destructuring on a Date object but **this doesn't work:**
   
   ```javascript
   //regular way
   const year = date.getFullYear();
   const month = date.getMonth();
   const day = date.getDay();
   
   //ES6 destructuring
   const {getFullYear(): year, getMonth(): month, getDay(): day} = date; //doesn't work
   ```
   I guess you can't use it this way because these are functions- `getDate()`, `getMonth()`, `getFullYear()`. Maybe you can only use properties. I'm not sure yet. I didn't look furthur into it.
   
   ## Finished `tweetSearch()`
   
   I finished `tweetSearch()` but I changed the name to `searchString()`. 
   
   `searchString()` takes an object and outputs an object. Since it outputs an object I think I might want to change it to a class since it's sort of it's own type of data. 
   
   `searchString()` takes an object with properties for `since`, `until`, `range`, `participants`, and `num`. Some of these properties are optional. You can't do `range` and `until` in the same call because range and until are both used to calculate the the last day to search but in different ways, so they conflict.
   
   I thought I could search tweets from fellow participants but it seems like there's a limit on how many accounts you can search from. Since the number of people who joined #100daysofcode when I did was 120, that's too much for twitter to search. It looks like it has to be 24 or less. Something like that. But it looks like it also depends on what else you're searching.
   
   >Twitter handles fairly simple queries really well, but tends to break with longer and more complex queries.
   
   from [*unionmetrics.com*](https://unionmetrics.com/resources/how-to-use-advanced-twitter-search-queries/)
   
  ***The way you use `searchString()` would be like this:***
  
  ```javascript
  let search = searchString({since : mySetOf100.startDate, until: mySetOf100.dateOf(10), participants: mySetOf100.participants, num: 3}) //creates an object
  ```
  That create search object. It contains different strings, that are formatted to search twitter.
  
  To create a url you call the method `.url` and pass the properies, which contain different search strings, and organizes and encodes them into a uri.
  ```javascript
  search.url({search.since, search,until, search.num})
  ```
   Now that I'm talking through this I'm not sure if this is the best way to organize `searchString()` because what if, for example, I want to change the day number? I could just update `search.num` I guess. But I'm wondering if it would be better for the number string to be a function. Oh man, I'm not sure. I think this is fine for now. When I start using it maybe I'll get a clearer picture.
   
  ## Applying for a twitter API key
  
  I got some advice on applying for an API key from two people who got it. 
  
  [Vipul Bhardwaj](https://twitter.com/vipulbhj) said:
  
   >I mean I told them,
   >what I am gone use this for (Personal Project)
   >How will I use the data( In detail) 
   >
   >and other answered questions. In a few days got my API key
   ...
   >Be as detailed as you can be. And you are good to go
   
   [Anders Magnus Andersen](https://twitter.com/izznogooood) said:
   
   >I did it a month ago and remember this, it's the link they ask for which they say can be empty. But it can't, use https://twitter.com 
   
   So I'm going to try and reapply. I want an api for this project and for a lucid dream twitter bot.
   
  ## Do to:
  
  Now I have to get the date from the user. I could use some drop down calender. Not sure. Maybe I should start simple with a string and a function that turns the string into a date and sends the date to make a OnHundredDaysCode set. 
  
  I also want to change `searchString()` to a class.
  
  **Thoughts and Feelings:** Good day, can't complain. Coding is fun when I'm working on my own projects. The time flies so quickly. Can't wait untill I'm able to work more than 2 hours a day on coding. I'm limited by my power supply right now. But I also limit myself to two hours because this is a method for building a solid habit. If you limit yourself, you'll want to do more. You'll think about the habit because you're not allowed to do it which makes you want to do it more. Then when I'm finally allowed to work more than two hours, I'll be so happy. I think about code alot now. I code in my head outside the two hours, no rules against that! I code in my dreams too.
  
**Link to Work**: [100Daysofcode Tweet search](https://github.com/dangerousdashie/100daysodcode_post_search/blob/1701c23a151cb22e1fc6883a51ec4daa08ccca55/class_test.html)
   
## Day 74
### 3/15/19

- ## **100Daysofcode Tweet Search Project**

  I had an issue with my `numberOf(Date)` function. The days were calculating wrong so I fixed that.
  
  ## Arrow functions and the `arguments` object
  
  I found out arrow functions don't have the `arguments` built in parameter:
  
  >Arrow functions don't have this since the arguments array-like object was a workaround to begin with, which ES6 has solved with a rest parameter:
  >
  >`var bar = (...arguments) => console.log(arguments);`
  
  from [*stackoverflow*](https://stackoverflow.com/questions/41731854/why-do-arrow-functions-not-have-the-arguments-array)
  
  What is a rest parameter?
  
  >The rest parameter syntax allows us to represent an indefinite number of arguments as an array.
  
  from [*the Mozilla Documentation*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/rest_parameters)
  
  ## Organizing my `tweetSearch()` function
  
  I spent a lot of time thinking through the `tweetSearch()` function. 
  
   - How should it be organized?
   
   - Should it use dot notation?
   
   - Should it be a method in the OneHundredDaysCode class?
   
      - I thought probably not, since we might want to use it without a set of OneHundredDaysCode
      
   - At one point I even wondered if it should be a class
   
  For now, I settled on it being a function that takes an object, this way not all the parameters need to be passed. I can create a string with different parameters, sometimes date, sometime particpants, etc.
  
  I'm ***thinking through the structure very slowly*** because the structure of the code is what I'm focusing on right now. ***How exactly should code be organized so it is reusable and clean?***
  
  It helped to jot down what my inputs might be and what the output would be for `tweetSearch()`. This helped me think about the calculations that would have to go between the inputs and outputs. It also helped me understand how to take the inputs.
  
  ![screetshot](log_imgs/notes_3-15.PNG)
  
  For example, I realized it didn't make sense to pass an instance of OneHundredDaysCode to `tweetSearch()`. I should instead just except a few simple parameters - date, number of day, etc..- and I can use the instance of OneHundredDaysCode in question to get those parameters by accessing the properties such as `.startDay` or the functions like `.dateOf(number)`.
  
## Revisiting `.reduce()`

   I ended with trying turn an array of participants into the search string that can be used on twitter:
   
  -  **Inputs --> Outputs**
  
     **input:** ["DashBarkHuss","Dominus_Kelvin","mahakothuri"]
     
     **output:** "from: DashBarkHuss OR from: Dominus_Kelvin OR from: mahakothuri"
     
   I was going to use `forEach()` but then I thought reduce might be good. But  was having trouble figuring it out with `reduce()`. I'm not sure if `reduce()` isn't the best for this, if if it's just that I can't figure it out.
   
   I accidently lost my work for this anyways.
  
  ## Need to do: 
  
   I need to remember get rid of promoted tweets that come up in searches. And of course do everything else. 
   
## Thoughts and Feelings:
  
  I think thinking very slowly and delibertly through the planning process will prove fruitful. I hope that I will develop a natural sense of what classes, functions, modules etc..., I will need when I start projects.
    
**Link to Work:** [100Daysofcode Tweet search](https://github.com/dangerousdashie/100daysodcode_post_search/blob/27362d861530653b2c67c2063a084b681393e50b/class_test.html)
   
   

## Day 73
### 3/14/19

- **100Daysofcode Tweet Search Project**

  I finally took a minute to find the answer to the question:
  
  ### How do you delete the current line you're editing in DevTools or the command line?:
  - `cmd` +`U` 
  - `ctrl` +`U`
    
  ## Redoing my code
  
     I realized my code for this project was a mess. I didn't start with a good plan. Yesterday, I started to make a new version of the app that is more simplified. I continued with that today. 
     
     First I drew out flow of what the UX might look like. Obviously, these sketches are messy and I don't expect anyone be able to read these, but maybe it can give an idea of what I'm doing.
     
    ![screenshot](log_imgs/sketch1_3-14.PNG) 
    
    
    Writing this out helped me see where I might reuse the same functions. It helped me get an idea of how to organize my code into classes or modules or functions.
    
    Next I started to sketch out my classes and functions in tables like this:
    
    ![screenshot](https://i.stack.imgur.com/xaJMm.jpg)
    
    I don't know what you call this kind of sketching. I got the above image from this [stackoverflow thread](https://stackoverflow.com/questions/3085285/difference-between-cohesion-and-coupling) about coupling and cohesion. The thread is useful in explaining how to best organize your code: basically ***avoid* coupling**, and ***use* cohesion**.
    
    Here, I started to sketch out my objects. I referred back to my UX sketch to get an idea of how to organize my code. Again, I don't expect anyone will be able to decipher my messy handwriting. 
    
    ![screenshot](log_imgs/sketch2_3-14.PNG)
    
    I didn't finish the planning yet.
     
    ## Revisiting Classes
    
    I wanted to use classes in this rewrite of my app. I used classes before in tutorial projects, but not on my own much. I decided to review classes and make sure I could write one. 
    
    Here's the [Mozilla documentation on javascript classes](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes)
    
    I wrote out a class and tested it out in the console and edited it until it worked.
    
    ```javascript
    class OneHundredCodeDays {
        constructor(date, num = 1){
            this.startDate = new Date(date); //what type is date? it's a Date so you have to get the date first
            this.endDate  = new Date(date);

            this.startDate.setDate(date.getDate()-(parseInt(num)-1)); //parse int just incase we got it from a string
            this.endDate.setDate(date.getDate()+(100-parseInt(num))); 
        }

        dateOf(num){
            let date = new Date(this.startDate); 
            date.setDate(this.startDate.getDate()+parseInt(num)-1)
            return date;
        }

        numberOf(date){
        return Math.ceil((date-this.startDate)/86400000);;
        }
    }
    ```
    I can construct an instance of this class for example that creates a set of 100 days starting on my start day:
    
    ```javascript
    const myStart = new Date(2019,00,01); //jan 1, 2019
    const mySetOf100 = new OneHundredCodeDays(myStart);
    ```
    
    Then, for example, I can get what day out of 100 it is for me today:
    
    ```javascript
    const today = new Date();
    mySetOf100.numberOf(today); //returns 73
    ```
    
## Thoughts and Feelings:
  
  I've had ***8 dreams this year about coding.*** That's 8 dreams in only 3.5 months. Last year, I had **one** dream about coding the entire year! This increase is great because you consolidate memories when you dream. That means you solidify the thoughts or ideas that you dream about, moving them from short term memory to long term memory. 
  
  These dreams weren't lucid but they were still useful. They make my brain better at coding. I hope to have more. I can also think about coding in my lucid dreams.
  
  ![screenshot](log_imgs/codedreams_3-14.jpg)
    
**Link to Work:** [100Daysofcode Tweet search](https://github.com/dangerousdashie/100daysodcode_post_search/blob/ba6cb3ee290c368c1c38806a35b4bf2d2712379d/class_test.html)

## Day 72
### 3/13/19

- **100Daysofcode Tweet Search**
    ## Function Declarations vs Expressions
   I started by changing my function declarations to function expressions so all of my functions would be the same format. But this lead to some errors. This is because [function declarations are hoisted](https://javascriptweblog.wordpress.com/2010/07/06/function-declarations-vs-function-expressions/) but function expressions aren't always hoisted.
   
  >**Do Function Expressions get Hoisted too?**
  
  >That depends on the expression. Let’s look at the first expression in Question 2:
   
  > var bar = function() {
  >  return 3;
  >};
   
  >The left hand side (var bar) is a Variable Declaration. Variable Declarations get hoisted but their Assignment      Expressions don’t. So when bar is hoisted the interpreter initially sets var bar = undefined. The function definition itself is not hoisted. 
  
   *from [javascriptwebblog, Function Declarations vs. Function Expressions](https://javascriptweblog.wordpress.com/2010/07/06/function-declarations-vs-function-expressions/)
   
   ## Git: revert
   So I had to revert to undo what I commited so I followed [this page about `git revert`](https://code.likeagirl.io/how-to-undo-the-last-commit-393e7db2840b). First it told me to `git stash` but I didn't know what that was so I [read about it here](https://www.git-tower.com/learn/git/faq/save-changes-with-git-stash). Then, [following this page again](https://code.likeagirl.io/how-to-undo-the-last-commit-393e7db2840b) I did `git revert`.  I ended up stuck in this thing on the terminal:
 
  ![screenshot](log_imgs/git_3-13.png)
 
  I got out of it by following [these instructions from githowto.com](https://githowto.com/commiting_changes):
 
  >On the first line, enter the comment: “Added h1 tag”. Save the file and exit the editor (to do it in default editor, press ESC and then type :wq and hit Enter).
  
  Obviously, put your own comment in, not “Added h1 tag”. I still found it kind of wonky to enter the commit comment.
  
  [](https://stackoverflow.com/questions/17665489/using-this-inside-an-event-handler)
  
  ## Event Handlers and `this`
  I was inside an event handler and `this` was returning as the global object. I knew that `this` is supposed to the same as the current target of the event object, which can be found in the `currentTarget` property of the event object. `currentTarget` is the element that the event listener is set on.
  
  ```javascript
  document.querySelector("#myDiv").addEventListener('click', handler);
  ```
      
  For example, in the above, the element with id `#myDiv` will be the current target. `this` will also be `#myDiv.`
  
  ```javascript
  function handler(e){
  e.currentTarget //returns #myDiv
  this            //returns #myDiv
  }
  ```
  ***So why was `this` the global object, aka the browser window, in my handler instead of the current target?***
  
  To figure out the problem, I isolated the issue into a seperate file. 
  
  This file had a div with id `#testDiv` to set the event listener on. The event listener called the handler on `click`. I copied my function but replaced the body with `console.log(this)`. I still got the global object.
  
  ```javascript
  const this = (e)=>{
  console.log(this); //logs the global object
  }
      
  document.querySelector("#testDiv").addEventListener('click', handlerFunctionExpressionArrow);
  ```
  
  Because I isolated my issue, I could see the issue a little more clearly. I remembered that arrow functions handle `this` differently than regular functions. So I changed my test code:
  
  ```javascript
  const arrowFunction = (e)=>{
      console.log(this);        //global object
  }
  const regularFunction = function(e){
      console.log(this);        //current target, aka #testDiv
  }

  document.querySelector("#testDiv").addEventListener('click', arrowFunction);
  document.querySelector("#testDiv").addEventListener('click', regularFunction);
  ```
 
  So the issues was, I needed to use regular function if I want `this` to be the current target.
  
  More info on `this` and arrow functions:
  
  > Value of this inside an arrow function is determined by where the arrow function is defined, not where it is used.
  [from StackoverFlow](https://stackoverflow.com/questions/36915875/javascript-arrow-functions-this-in-event-handler)
 
  ## What Next:
  
  The program now takes the array of #100daysofcode particpants from the first seach, and if you press submit you can then press a search button and it will automatically copy the code you need to compare the two sets of participants. I don't have the instructions up for this second part so it looks kind of confusing but it works.
  
  My code needs to be more reusable. I'm wondering ***how to better plan code when I start*** so I don't have to do a crazy amount of refactoring in the middle of my projects. I need to think ahead. I think the more experience I get the better I will be able to figure out how to plan my code. But maybe I can research how others approach this.
  
  I started to make a new version of this twitter search app that is more simplified. It will just give the user a list of people that started 100daysofcode on the same day as them and are still doing 100daysofcode. I started writing out the psuedo code. I want to do this simpler app because I think it will help me really plan what's going to happen at every step of the UX.

- ## **Thoughts and Feelings:** 
    Good day. I did a lot of note taking and documenting on my log because I ran into a bunch of little issues and I wanted to remember what I learned.
    
**Link to Work:** [100Daysofcode Tweet search](https://github.com/dangerousdashie/100daysodcode_post_search/tree/c3cfdf95ba95a76bdcf2c1b4307f7c6e777e654d)

## Day 71
### 3/12/19

- **100Daysofcode Tweet Search**

    I made it so when you click the search buttons, the program automatically copies the code you need to the clipboard. I used [this code snippet on hackernoon](https://hackernoon.com/copying-text-to-clipboard-with-javascript-df4d4988697f).
    
    I changed a lot of little things:
    
    - I removed the div that held the code that you need to copy and paste, since now it is automatically copied.
    
    - I added a list of instructions. 
    
    - I refactored some of the code.
    
    - I made specific dynamic strings for each search button.
    
    - I changed the HTML syntax so I could use the `'submit'` event lister instead of `'click'`.
    
    - I started to make a form that returns what to paste so that you can find the common posters between two searches. This way you can find all the people that started on one day and are still posting today for example.
    
    I'm forgetting some of the things I did. Next time I'll make a list of everything I do or I'll commit to git more to track my changes.
    
   ## Before:
   ![screenshot](log_imgs/twitter_b4_3-12.png) 
   
   ## After:
   ![screenshot](log_imgs/twitter_after_3-12.png)
    
    **Things to do:** design, localStorage, finish the form to find common particpants between two searches. 
    
- ## **Thoughts and Feelings:** 
    I decided to let myself sleep in to see if I needed any sleep. ***I was able to sleep 10hrs and 45min.*** So I must have been in a bit of sleep debt. I felt so much **better, happier, and motivated today.**
    
    Catching up on my sleep *gave me back my power.* 
    
    The past few days I felt **depressed and hopeless**. Just a few days before I was **so incredibly happy for months.** My sleep deprivation zapped my energy that I normally have to deal with difficult situations. It took away my motivation to do the happiness practices I normally do to maintain my emotional health. Instead my brain took the path of least resistance, defaulting to ruminations on the negative. I felt hopeless even though intellectually I knew on some level my situation couldn't be hopeless. Thankfully, some part of me knew this feeling would pass.
    
    Today, after 10h:45m of sleep, the sleep itself instantly changed my outlook upon waking. But it also gave me the energy to do my happiness practices again, which further helped my emotional state. 
    
    ***This was the practice I did:*** Everytime I thought something negative, I had to subtract a point on a notepad and write next to it the subject I was thinking about. Then, I had to think of three nice things about that subject. I got to negative 5 which also meant I had to think 15 nice things about those negative subjects. 
    
    After the first hour or so of the day, the negative thoughts were exterminated. I'm excited to code and I'm incredibly happy again and my ruminations are gone.
    
    ***What I learned or relearned:*** 
    - Sleep is incredibly important for me. I think I need to figure out how to avoid sleep debt, especially for more than a day. I thought it would be nice for my family if I stayed up late to hang out with them. But the compounding sleep debt made me depressed and probably not my best self for them. So it really wasn't worth it.
    
    - I may enter into a funk, but it's just temporary. 
    
    - My happiness practices are so important.

**Link to Work:** [100Daysofcode Tweet search](https://github.com/dangerousdashie/100daysodcode_post_search/blob/846110c162ed5b3ac83134df45ca50292630911d/index.html)

## Day 70
### 3/11/19

- **100Daysofcode Tweet Search**

  Before, I had this project output strings like: ***"(Day1 OR “day 1” OR r1d1 OR “r1 d1”) (#100daysofcode) since:2019-01-01 until:2019-1-8"*** and you were supposed to copy and paste them into the twitter search bar:
  
  ![screenshot](log_imgs/twitter3_02.gif)
  
  I replaced those output strings with links so now there's no copying required for the searching. 
  
  I added a custom day range option. This way I can seach everyone who posted "day 1" starting January 1st and ending January 10th for example, if I put 9 in for the custom day range.
  
  ![screenshot](log_imgs/twiiter3_11.gif)
  
 - **Thoughts and Feelings:** I coded while in the passengers seat of the Rialta today. It was hot. I learned a lot today, but I'm so hot I'm just trying to finish this post so I can get outside the Rialta and cool off. So, not doing an indepth post today.
  
   ![screenshot](log_imgs/driving3_11.gif)
  

**Link To Work:** [100Daysofcode Tweet search](https://github.com/dangerousdashie/100daysodcode_post_search/blob/5c72bb19324ecb0d5ca42ba2efefdade244ac5a5/index.html)

## Day 69
### 3/10/19

- **JavaScript30: *Tally String Times with Reduce***

   For this project, no offense Wes, but I liked my solution better than Wes's! But here are somethings I liked from his solution that were either new techniques or refreshers:
   
   ### Making an Array from a NodeList
   
   We can't use array functions on NodeLists, except for `forEach()`. Wes said using `forEach()` to turn a nodelist into an array is messy so these two ways are better:
   
   - #### Spread Operator:
   
         const timeNodes = [...document.querySelectorAll('[data-time]')];
      
   - #### `Array.from()`:
      
         const timeNodes = Array.from(document.querySelectorAll('[data-time]'));

   ### Another Example of ES6 destructuring:
      let time = "3:45";
      const [mins, secs] = time.split(':');

      >mins //input
      <"3"  //ouput
      >secs
      <"45"  
   
   
   ### Passing a function into `map()`:
      let stringNums = ["60", "23", "90"]
      stringNums.map(parsefloat)
      <[60, 23, 90]
      
   ### `reduce()`:
   
   Ofcourse, we learned more about the array function `reduce()` in this video. Here's the [documentation for reduce.](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce)
  
- **#100daysofCode Twitter Participants:**

  I started to change the string to search that the program outputs into a link that you can just click and it will search the string for you.

 - **Thoughts and Feelings:** 
 
   I need to find a way to get spell check on this markdown editor on github. I keep spelling words wrong. 
   
   I accidently coded on less sleep. I was trying to wake up early to get coding done so I could hangout with my sister. So I set my alarm to sleep 7 hours which I thought would be the minimum I could have without feeling tired. But I forgot it was daylight savings. I was so tired when I woke up. I actually only set my alarm to sleep 6 hours. I didn't realized it until later. I had real coffee today and it's giving me a tiny bit of acid reflux. 
  
   
## Day 68
### 3/9/19

- **JavaScript30: *Sort Without Articles***
   
   This took me a while because I'm still getting used to the array functions like `map()`, `sort()`, and `filter()`. 
   
   I also got stuck for a while because I meant to use `indexOf()`, but because I was thinking of indexes I did `indexOf[]` instead of `()`. I'm surprised I got no syntax errors from this. 
   
   I learned a little bit about [in place](https://en.wikipedia.org/wiki/In-place_algorithm) algorithms when I was reading about [`sort()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/sort). I was wondering where the sort function keeps the order of the array at every step? I guess it stores it **in place** which I'm trying to understand. Is it accessible in the debugger? If I want to see mid-sort-function what's been sorted and in what order so far, can I? 
   
   I'm trying to figure out what order the compare function in `sort()` takes each item. I console logged an example to see but I'm still not sure.
   
   ![screenshot](log_imgs/array_3-09.png)
   
   
   When I watched Wes's solution, it was much better. He used regular expressions with `replace()` which made the program much more concise. I ended with trying the challenge over again, doing what Wes did. 
   
   Learned about `trim()` which removes white space from the outer edges of a string.
   
 - **Thoughts and Feelings:** 
    
    I journaled last night to help me figure out how I could have better handled the situation that happened yesterday. I feel much better and figured out what I can do in the future. 
    
    This morning I meditated and that helped too. I didn't do mindfulness which I normally do. Instead, I tried a meditation from the book I'm reading, which claims mindfulness isn't the same as meditation. In my case, meditation was a better choice because it helps rejuvinate you and get rid of passed stress. It prepares you to handle stress in the future. Mindfulness is mostly just for the present moment.
    
    Coding was much easier today. My extended family wasn't around. I coworked with my cousin by the pool under a "computer burqa" to help reduce my eye strain.
    
    ![screenshot](log_imgs/cowork_3-09.jpg)
    
Grateful to be feeling good. I had a great coding session. Yesterday night, I had so much fun playing pictionary with my family. My sister is flying in today! As long as I can respect my coding time and my family time, I know I'll have a good time.
   
## Day 67
### 3/8/19

- **Helping People**

   Today I spent the day opening my inbox up to help people. My tweet was retweeted by @js_tut and so I got a lot of messages. I thought I would get more questions related to fixing bugs so I thought I would do more coding but people asked more for general advice. I got a surprising amount of questions from people overseas which can be hard because there was sometimes a language barrier. Very few women asked me questions, 2 out of 13. Ladies, come on! I don't know if it's because there's just less female coders (it doesn't seem like it online though) or if women ask for help less. I think Americans in general are afraid to ask for help which is why I got more questions from foreingers. I'd like to come up with a better way to help people.
   
- **JavaScript30: *Sort Without Articles***
  
  I decided to do a bit more coding since I didn't really code when I was helping people. I started the *Sort Without Articles* challenge from JavaScript30. I got stuck on using the array sort() function for a really long time. I was coding around family on vacation so I was pretty distracted. At first I thought it had to return true or false, but the sort function has to take a function that returns 0, a positive value, or negative value. Once I figured that out I thought that you could have a return statement in a ternery statement. But a ternery statement requires expressions, **not statements**. Then I thought arrow function had implied returns but apparently I needed the return statement because I had the expression in a block: 
  
  >Arrow functions can have either a "concise body" or the usual "block body".
  >
  >In a concise body, only an expression is specified, which becomes the implicit return value. In a block body, you must use an explicit return statement.
  
  -- from [*Arrow functions*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)

  
  Oh the little things! I finally got the syntax down:
  
      const bands = ['The Plot in You', 'The Devil Wears Prada', 'Pierce the Veil', 'Norma Jean', 'The Bled', 'Say Anything', 'The Midway State', 'We Came as Romans', 'Counterparts', 'Oh, Sleeper', 'A Skylit Drive', 'Anywhere But Here', 'An Old Dog'];

      var bands2 = bands.sort((a,b)=>{
           return a.charAt(a.length-1) < b.charAt(b.length-1) ? -1: 1;
      });
     
   This sorts the array in alphabetical order by the last letter in the band name.
   
 - **Thoughts and Feelings:** Having a hard time on family vacation. My family's getting mad at me for coding at times. I'm distracted. I need to relax, it was upsetting me that they got mad at me for coding instead of setting the table. I said I would do it in a few minutes after I finished coding and they got mad. It hurt my feelings a lot. I don't know! 
   
      

## Day 66
### 3/7/19

- **JavaScript30: *LocalStorage And Event Delegation***

   - I reviewed what I learned in this video, practicing the concepts again in a simple HTML document. 
   
   - I finished the additional challenges for this project to create buttons that clear, uncheck, and check all the options.

- **JavaScript30: *CSS Text Shadow Mouse Move Effect***
   
    I learned about the `offsetX` and `offsetY` property on mousemove events, which shows where the mouse was when the event was triggered. I learned about ES6 destructuring:
 
    
    ### ES6 Destructuring
    What is ES6 Destructuring?
    
    >It allows us to extract properties from an object or items from an array, multiple at a time.
    
     -- from Wes Bos's [*A Dead Simple intro to Destructuring JavaScript Objects*](https://wesbos.com/destructuring-objects/)
     
     Old way
      
      const width = hero.offsetWidth;
      const height = hero.offsetHeight;

     New way

      const { offsetWidth: width, offsetHeight: height } = hero;
       
     Fun project.
     
     ![screenshot](log_imgs/javascript30_3-07.gif)
     
 - **Thoughs and Feelings:** 
   
  I was pretty distracted today. I slept late on purpose to make up for my sleep deprivation. I feel great. But that pushed my coding back. So I had to code around my family since I'm currently on a family vacation. I'd prefer to get my coding done early before I see them while I'm on this vacation.
  
  I was also distracted by the boom of followers I got today. @js_tut tweeted this:
       
   ![screenshot](log_imgs/follow_3-07.png)
   
  So a bunch of people followed me and it was exciting and distracting! 
  
  I think I need to go relax and clear my mind!
  
**Link to Work:** [Review of localStorage concepts](https://github.com/dangerousdashie/JavaScript30/blob/c9123dfc403e26f9e443e9675bd0778d6bb39465/15%20-%20LocalStorage/index-review.html), [LocalStorage additional challenges](https://github.com/dangerousdashie/JavaScript30/blob/c0540917c095f4a5f676dd18ad1de7dd375ad7ec/15%20-%20LocalStorage/index-dashie.html), [Mouse Move Effect](https://github.com/dangerousdashie/JavaScript30/blob/6897ca4557c38bc9397d10ba2d2672056082f362/16%20-%20Mouse%20Move%20Shadow/index-dashie.html)

## Day 65
### 3/6/19

 - **JavaScript30: *LocalStorage And Event Delegation***
   
   This video had so much going on. I enjoyed it and went slowly. It was a 30 minute video but I took 2 hours to go through it. I learned a lot. This was the first time I used localStorage. I can't wait to use it in my own projects. Here were some other interesting things I learned:
 
   ### Event Listeners: Submit Vs. Click
    - Use *submit* on a **form**. If you just add a click event to the form button it won't work when someone presses enter.
    - With submit the default is for the page to refresh, so you may want to use `preventDefault()` in your event listener callback function.
    
    ### ES6 Property Value Shorthand
    
    Let's say we have a variable we want to use in making an object:
    
       const text = "some text here";        
        
    **Initializing the variable:**
    
    Old way
            
        const item = {
            text: text,
            someOtherProperty: "someValue"
        }
    New Way
       
       const item = {
            text,
            someOtherProperty: "someValue"
        } 

   You can leave out the property value if the key matches the variable name.
   
 - **Thoughs and Feelings:** 
   
   I am slightly sleep deprived. I had one night of 5 hours of sleep and a few of 6-7. I really like to get 7.5-9. For some reason I've been waking up really early. Then I'm too excited for the day and can't go back to sleep. I'm hoping that when I start #100DaysOfMeditation I can fix this.
   
   I could feel the toll the deprivation is taking on me. I was a little anxious to finish my coding. Sleep deprivation decreases my motivation. But I still did a pretty good job of staying focused and excited about coding, inspite of the lowered motivation. I'm also on family vacation so that might have played a roll in my motivation. I know my family wants to see me and yet I'm in my hotel room coding.
   

**Link to Work:** [LocalStorage And Event Delegation](https://github.com/dangerousdashie/JavaScript30/blob/33d51f5b67e4e77dac5b622c2c61d347e4acea5f/15%20-%20LocalStorage/index-dashie.html)

## Day 64
### 3/5/19

 - **JavaScript30: *Object and Arrays - References VS Copy***
 
    I learned that when you **assign a variable** to an **object** identifier or an **array** identifier, the new variable is a *reference* to that object or array. So they are two different identifiers for the *same exact thing*. If you change an array item using one of the identifiers you are really changing the array that the other identifier referenced too.
    
    On the otherhand, when you **assign a variable** to a **boolean, number, or string** identifier, the new variable is a *copy* of the other one. If you change the new variable it won't affect the other variable.
    
    How can you create a copy of an array? These ways:
    
       const players = ['Wes', 'Sarah', 'Ryan', 'Poppy'];
       
       const team2 = players.slice();
       const team3 = [].concat(players);
       const team4 = [...players];
       const team5 = Array.from(players);
   
   How can you create a copy of an object?
   
       const person = {
          name: 'Wes Bos',
          age: 80
       };
 
       const cap2 = Object.assign({}, person, { number: 99, age: 12 });
       
   The copy is only one level deep. An object nested inside an object *will be referenced* when you use object assign, even though the main object is *copied*. ex:
   
       const wes = {
          name: 'Wes',
          age: 100,
          social: {
            twitter: '@wesbos',
            facebook: 'wesbos.developer'
          }
        };
        
       const dev = Object.assign({}, wes)
       
   `dev` is a copy of `wes`, but within this copy we made a *reference* to the nested `social` object.
  
   How can you copy more than one level? Find a clone deep function online. Some libraries have them. Wes said you should be cautious about using it because there's often a better way to deal with the situaion that cloning deep but I'm not sure why. He also gave this poor mans version of close deep but said he's not sure of it's affect on performance:
  
    const dev2 = JSON.parse(JSON.stringify(wes));
    
  - **CSS3 in 30 Days: *Style Fancy Buttons***

    I started CSS3 in 30 Days which was recommended to me on twitter. I didn't finish it but the first video was styling buttons. I don't know if I like this tutorial. 
    
    ![screenshot](log_imgs/van_3-05.gif)


## Day 63
### 3/4/19

 - **JavaScript30: *Slide In On Scroll***
   
   I forgot I finished this challenge so I did it all over again. I spent a way longer time on it, trying to make sure I understood how all the heights of everything work together. It helped to step away from the monitor and sketch out how it all worked.
   
   ![screenshot](log_imgs/scroll3-04.jpg)
   
   I learned a short cut for changing a value quickly in DevTools. Click into a numerical css property value (ex: `transform: translateX(`**26.4%**`) scale(0.95);`) in the DevTools style tab. Then use the up and down arrow buttons or the mouse wheel to increase or decrease by 1. For other increments hold down the following keys:
    
   - `cmd` +/- 100
   - `shift` +/- 10
   - `alt` +/- .1
   
   I finished the challenge. Then I watched Wes do it and copied him. I ran into a bug; for some reason when I get the heigh of the image it says it's 0. but When I'm not in the forEach loop the image is the right height. More investigating tommorrow.
   
 - **Thoughs and Feelings:** 

   I am at my cousin's house. I felt self-concious working here. The other day my cousin asked my spouse "Aren't you in tech?" and what he was doing to make money. When he said "no" and "nothing", she was surprised and wondered how we would make a living. But she didn't ask *me*. *I'm the one who's in tech* and I'm going to be making the money. I felt self-concious, like why didn't she ask me? Do I give off a vibe that I'm lazy and good for nothing? I got in my head. When I was working today I really wanted her to notice I was working on coding but she never asked me. I felt lame for caring about this. I know it's not important so this is something I need to work on. I really wanted my hard work to be recognized and not to be seen as lacking ambition. But I shouldn't care what others think. My focus should be on my own goals.

   I have to remember that this is a universal feeling. Probably all the people I look up to feel like this sometimes. I have to remember that these feelings are just in my mind. To help, I can consider alternative interpretations of what happend: My cousin doesn't know anything about my ambitions and doesn't want to offend me by asking me what I plan to do. A lot of times men are the bread winners and women aren't so maybe she didn't want to insult me and imply that I had to work like she does. Maybe she knows I study code and so she didn't ask. Worse case scenario, if she does think I'm lazy, she still thinks I'm a great person and she will find out soon enough that I'm a hard worker when it's obvious. And then it will be a fun surprise. it's kind of fun to surprise people about who you really are.
   
   These thoughts and feelings distracted me. When I work in an office I might encounter similar feelings so this is something I really want to work on so I can stay focused.

## Day 62
### 3/3/19

- **Pomodoro:**
   
   I continued to work on my Pomodoro app. I worked on the design, layout, css, etc. Again, I used [this dribbble page for inspo.](https://dribbble.com/shots/2750480-Daily-UI-014-Countdown-Timer) 
   
   I refactored some code for the `makeSecondsReadable()` function so it was more reusable. I reused it for the interval displays.
   
   I added specific css for when the timer is on a break interval. The timer turns to a green/blue gradient.
   
   I still need to add more form validation, get the code to work with minutes submited in m:ss (1:30) format instead of just decimals (1.5), and pick better alarm sounds.
   
   ![screenshot](log_imgs/pomo3_03.gif)
   
**Link to Work:** [Pomodoro, last commit](https://github.com/dangerousdashie/pomodoro/tree/169083e0c7bb825091ea66811ba2af49770053d8)
   

## Day 61
### 3/2/19

   Yesterday I coded more after I posted to my log. I did more with my **#100daysofCode Twitter Participants** project. Today I continued with that and took a break from my **Pomodoro** App. I did get an answer to my Pomodoro questions on [stackover flow](https://stackoverflow.com/questions/54949659/white-line-appears-at-end-of-gradient-filled-div-at-specific-browser-widths). The line was due to a rounding issue. basically it's calculating a percentage of a width that has a decimal. Since it's not a whole number, it's sometimes rounding it up, sometimes down, not sure exactly. The trick to fixing it is to set the background size slightly bigger than the div, `background-size: 100% 101%;`
   
- **#100daysofCode Twitter Participants:**
   
   I got this project pretty far. It's usuable but the design isn't great. I need to redo the layout so I have more space for instructions. I haven't put it on git hub yet, but I will after the instrucitons are done. 
   
   You can see how someone might use it in the video below. In this example, I used it to find out how many people started #100daysofcode the same day as me: I enter a date (march 1st) and the day of #100DaysOfCode (60th) because I was on my 60th day yesterday. The program outputs 3 string queries to search on twitter: one for the day entered, one for the supposed start day for these people, and the end day for them. 
   
   I copied the _'start day'_ string query to see how many people started the same day as me. I searched the string query on twitter. It shows me all **day 1 #100DaysOfCode** tweets of the people who started on January 1st, when I started. I selected the 'latest' tab. Scrolled all the way down so all the tweets loaded. Then I used the console to find the number of people who started with me, using code I saved on the project UI for easy copying.
   
   ![screenshot](log_imgs/twitter3_02.gif)
   


## Day 60
### 3/1/19

- **Pomodoro:**
   
   I posted my white line problem [here on stackover flow](https://stackoverflow.com/questions/54949659/white-line-appears-at-end-of-gradient-filled-div-at-specific-browser-widths).
   
   ![screenshot](log_imgs/pomo3_1.gif)
   
- **Helping out:**   
   
   I helped another #100daysofcode participant on her [coding homework.](https://twitter.com/ItsladyKadie/status/1101475054460854272) I've been wanting to help someone and I finally did!
   
- **#100daysofCode Twitter Participants:**
   
   I started playing around with a script that finds how many #100daysofcode participants for a certain day (day 1/100. 2/100 etc) on a certain date there are.
   

## Day 59
### 2/28/19

- **Pomodoro:**
   
   I finished the function that converts the time from seconds to m:ss. I merged my branch into to master and deleted the branch. 
   
   I worked on the layout of the timer. I reviewed some of [flexboxfroggy](https://flexboxfroggy.com/) but I decided to change the flexbox I was working with to grid. I'm still trying to get the hang of each. 
   
   I added some styles. I used [this dribbble page for inspo.](https://dribbble.com/shots/2750480-Daily-UI-014-Countdown-Timer)
   
   _dribbble inspo_
   ![dribble inspo image](https://cdn.dribbble.com/users/455244/screenshots/2750480/daily-ui-_014.png)
   
    _Work by [Serj Krush](https://dribbble.com/SerjKrush)._
    
    I left off with a bug where the gradient background color has a white line on the right side of the timer div. I don't know why. It looks light pink in the gif, but it's white.
   
   ![screenshot](log_imgs/pomo2_28.gif)


**Link to Work:** [Pomodoro, working](https://github.com/dangerousdashie/pomodoro/tree/449255677c3e5a1985007de89383c4aeb3621312) (aka **not finished**, I just realized calling it **'working'** might be confusing)

## Day 58
### 2/27/19

- **Pomodoro:**
   
   I spent some time reading about asynchronous functions and promises after coding yesterday because of the error I had yesterday. But, thanks to a fellow participant of #100daysofcode, I figured out my `Uncaught (in promise) DOMException.` [@montyDev_](https://twitter.com/montyDev_) replied to my tweet:
   
   >Maybe it has to do with the new chrome policy about autoplay, i had the same error when i was building the simon game. Try testing it in other browsers as well
   
   So I tested in Firefox and I didn't get an error. In Safari I got a similar error but it had more information. So I searched it and @montyDev_ was right. I found out that both Chrome and Safari have restrictions on autoplaying audio elements. 
   
   From [bitmovin.com](https://bitmovin.com/play-not-play-new-autoplay-policies-safari-11-chrome-64/) on Chrome's autoplay policy, bold added for emphasis:
   > ...unmuted **autoplay requires any of the following conditions** to be fulfilled:

   > - **A user interaction with the website is required**
   >   - clicking anywhere on the document, navigation, …
   >   - scrolling is excluded as a valid user interaction in this context
   > - MEI (Media Engagement Index) threshold has to be crossed (Desktop only)
   > - User has added a PWA (Progressive Web App) to their homescreen (Mobile only)
   
   Safari has similar requirements, more info can be found [here on bitmovin.com](https://bitmovin.com/play-not-play-new-autoplay-policies-safari-11-chrome-64/)
   
   I realized I don't have to change anything since my single page app requires the user to interact before the audio plays. It was only happening in this instance because I had a test running so I didn't have to interact with the browser.
   
   I refactored my code in a new branch so that the input for displays are created in a function `inputForm()` so that I can reuse this code when the user presses edit. I have a working edit button now. 
   
   I started to make a function that makes the minutes into a more readable format. Instead of 2 minutes being 120 seconds, I want it to display 2:00. I still have to finish this and merge my new branch.
   
**Link to Work:** [Pomodoro, working]( https://github.com/dangerousdashie/pomodoro/tree/f91b48856bfa120f3ffb53effe87c1fc5037e375])

## Day 57
### 2/26/19

- **Pomodoro:**
   I dabbled in some flex box stuff for the pomodoro, changes the cursor in the css, and got the mute/unmute button working. But I ran into an error: `Uncaught (in promise) DOMException.` It looks like this error happened sometimes it got to line 190, `pomo.isWork()? document.querySelector(".tom").play() : document.querySelector(".tink").play();`. It didn't always happen, but when it did happen it looked like the error would then continue to happen every time it got to that line from there on unless I paused the pomodoro and started again. It seems like pausing and playing can change whether the error occurs but pausing and playing doesn't always make/stop the error.
   
   ![screenshot](log_imgs/pomo2_26.gif)
   
   I didn't understand much of what this means: `Uncaught (in promise) DOMException.`. So I thought I should review. I looked into errors in javascript, DOMException, promises. I still am confused and need to understand more. Here are somethings I came across:
   
   DOMException- "The DOMException interface represents an abnormal event (called an exception) which occurs as a result of calling a method or accessing a property of a web API. This is basically how error conditions are described in web APIs." -from [*DOMException, developer.mozilla.org*](https://developer.mozilla.org/en-US/docs/Web/API/DOMException)

    Read about the throw, try, and catch statements, but it never mentions what an uncaught error is. [JavaScript Errors - Throw and Try to Catch](https://www.w3schools.com/js/js_errors.asp)

    "When a JavaScript statement generates an error, it is said to throw an exception.  Instead of proceeding to the next statement, the JavaScript interpreter checks for exception handling code. If there is no exception handler, then the program returns from whatever function threw the exception.  This is repeated for each function on the call stack until an exception handler is found or until the top level function is reached, causing the program to terminate." [Exceptional Exception Handling in JavaScript
](https://www.sitepoint.com/exceptional-exception-handling-in-javascript/)

   "Uncaught means the error was not caught in a catch statement..." [Fix JavaScript Errors](https://davidwalsh.name/fix-javascript-errors) Now I'm starting to wonder if all non-user defined errors are uncaught and I never noticed it before. Are there ever built-in catch statements that the user doesn't define? Maybe in some Web API's?

   [This stackoverflow thread about uncaught in promise errors](https://stackoverflow.com/questions/50474287/uncaught-in-promise-error) looks like it might be helpful. It looks like it gives a way to handle these cases, so maybe I can log more info when the error occurs: "You can also handle these cases in your application with the unhandledrejection event."

   I tried to read this article on [error handling in javascript](https://www.sitepoint.com/proper-error-handling-javascript/) but there were a lot of references to unit testing in order to explain error handling. Since I'm not very familiar with unit tests, this wasn't very helpful. However, it's always good to take a look at other technologies I will eventually need to understand, so in that way it was helpful as it got my eyes and brain used to seeing unit tests. 
   
   [This page covers unhandled rejected promises](http://2ality.com/2016/04/unhandled-rejections.html) so I may get more info here. 
   
   I think tomorrow I will review promises. Asynchronous functions are still confusing to me. I will go on lynda for some videos about promises and maybe read [this article](https://hackernoon.com/javascript-promises-and-why-async-await-wins-the-battle-4fc9d15d509f) which I started reading today.

**Thoughts and Feelings:** I'm tired today so I'm taking more breaks. In addition, running into an error that I didn't understand was much more tasking than the coding I'd done in the previous days. I had to read a lot to understand the error. It was much more learning and studying, which are sometimes more draining than hands-on coding. When I code, the time flies, but when I read about coding I keep checking the timer wondering when my next break is. I want to improve my ability to learn, expecially my curiousity, so that I won't feel so drained when learning a new concept. Sometimes going very slow is helpful.

**Link to Work:** [Pomodoro, working](https://github.com/dangerousdashie/pomodoro/tree/a2d833bc6bfa4d4d6f42fd57bb8d57e41c2c6d8b)

## Day 56
### 2/25/19

- **Pomodoro:**
   
   I added sound to the timer. I finshed all my the controls (play, back, next) except mute/unmute. I still have to add an edit button to edit the pomodoro intervals. I also need to add nice design.
   
   ![screenshot](log_imgs/pomo2_25.gif)
   
**Link to Work:** [Pomodoro, working](https://github.com/dangerousdashie/pomodoro/tree/2b278b504f8074004bdc53cc1641705c71ae7310)

## Day 55
### 2/24/19

- **Pomodoro:**
   
   I merged my branch into master following [this video](https://www.youtube.com/watch?v=lsLjxSkGSDE). Don't forget to push to the remote! I deleted the old branch following [this](https://koukia.ca/delete-a-local-and-a-remote-git-branch-61df0b10d323).
   
   The pomodoro is pretty far along. I have the timer working. I still need pause/play/skip/back controls, an edit button, and to edit the css and design. I need to convert the numbers that represent seconds and minutes into a more readable format. 

   ![screenshot](log_imgs/pomo2_24.gif)

**Link to Work:** [Pomodoro, working](https://github.com/dangerousdashie/pomodoro/tree/6c2a35569b8f611649a9e77c9d0523976e03d6ad)
## Day 54
### 2/23/19

- **Pomodoro:**
   
   I tried adding an `.invisible` css class with `display:none` to some divs using `classList.add()`. It didn't work for one div that had an ID assigned to it that had `display:grid`. ID overides class. I finally realized the way to override it was to put `#<idname>.<classname>` (ex, `#inputs.invisible`) in the css. I found this out by playing around in Devtools in elements tab > styles tab. `#<idname>.<classname>` works because you're adding specificity. With this you can use `classList.add()` to add a class to a div that overides properties of the div's ID.
   
   I realized I didn't like the UX I came up with. I wanted to get rid of my `displayIntervals()` function. I created a new branch so I could experiment with getting rid of `displayIntervals()`. To do this I had to review git. This [page](https://github.com/Kunena/Kunena-Forum/wiki/Create-a-new-branch-with-git-and-manage-branches) suggested doing a git pull before making a new branch. So I reviewed what git pull does [here](https://www.git-tower.com/learn/git/faq/difference-between-git-fetch-git-pull), and continued with the rest of the [steps](https://github.com/Kunena/Kunena-Forum/wiki/Create-a-new-branch-with-git-and-manage-branches) until it said to create a new remote. On freecodecamp a slightly different approach was suggested. [Here on freecodecamp](https://www.freecodecamp.org/forum/t/push-a-new-local-branch-to-a-remote-git-repository-and-track-it-too/13222) the poster didn't include instructions on making a new remote for the new branch. I believe you can skip that step when you include the option `-u`; according to a poster on stackoverflow, when using `-u` ["tracking information has been set up so that git pull works as expected without specifying the remote or branch."](https://stackoverflow.com/questions/5697750/what-exactly-does-the-u-do-git-push-u-origin-master-vs-git-push-origin-ma) 
   
   I left off on my new branch, having created some display divs for the pomodoro timer. I will probably merge this branch tomorrow but I need to review merging.
   
**Thoughts and Feelings:** Working on a project from scratch is helping me learn about periferal technologies- git, github, css. Even though it's taking me a long time to finish this pomodoro project, I'm not phased. That's because I know I'm getting used to all the daily technologies I will use as a developer, not just the technology I set out to study, in this case javascript. It can seem disappointing when your first few projects take a while, but you are integrating all sorts of technologies into your brain. Therefore, all these "distractions" - having to figure out git, having to figure out a css issue, having to figure out the nuances of browsers, understanding your debugger, understanding your editor- these are all amazing learning opportunities. These are more important than the project itself. When your project is done you will have these technologies living in your brain to take with you to the next project. Be grateful for every bug, error, distraction. These are the challenges that make a difference. They build your skills.
   
   **Link to Work:** [Pomodoro no_display_intervals branch working](https://github.com/dangerousdashie/pomodoro/tree/76e000f75aca092c60b3a5d85975d1eb2aee4d12)

## Day 53
### R1D53 2/22/19
- **Pomodoro:**
   I worked on the css grid layout and the submit function. 
   
   I tried using `grid-gap:10px` with `grid-template-columns: repeat(8, 12.5%)`, but this adds 10px for each gap onto the 100% (8x12.5%) of the parent width. This ends up making the grid larger that the parent div. I switched to fr instead of percent `grid-template-columns: repeat(8, 1fr)` and this worked. 
   > Because grid-gap abstracts away the calculations it takes to add up to 100% of the size of the grid parent, it doesn’t play nicely if you take up that space with % units...It’s best not to use percentages in grid layouts. Instead, Grid introduces a new length unit, fr: a fractional unit. 1fr will occupy any available space in the row or column—it operates like % but in a way compatible with grid-gap without requiring any calc() fussiness. 
   
   [Rawk Blog, CSS Grid: Understanding grid-gap and fr vs. auto units](https://www.rawkblog.com/2018/03/css-grid-understanding-grid-gap-and-fr-vs-auto-units/)
   
   I left off on a function that displays the selected intervals. I'm having trouble overiding the display property for the `#inputs` div. The css for the `#inputs` id has `display:grid;`. I'm trying to temporarily set the display to `display:none;`. I have to review the order that the css is overwritten.
   
   **Thoughts and Feelings:** Today I took a stretch break every 15 minutes, a somewhat longer stand up and stretch break every 3 breaks, and made myself switch sitting positions every break. My back feels pretty good.
   
   **Link to Work:** [Pomodoro, working](https://github.com/dangerousdashie/pomodoro/tree/4c44dc156e325f57415c28f48460f45413fec796)
## Day 52
### R1D52 2/21/19
- **Pomodoro:**
   I refactored some code so that my function `createInput()` can be reused more often. I worked on the add button functions. I created the remove button function. I started the submit function. I left off trying to figure out how to ignore empty input fields when saving the input values.

   I spent a while trying to figure out scope and the debugger. Declaring block scope in console acts unexpectedly. If not paused in debugger, you can declare let and const variables and access them in the console.   


   ![screenshot](log_imgs/debuggerscope_2_21.png)
   
   But when paused in the debugger inside a script or in script tags you get an Uncaught Reference Error, the debugger thinks the variable is undefined. 
     
     
  ![screenshot](log_imgs/debuggerscope_unidentified_2_21.png)
   
  In my example I'm within a function, but to be clear, I got the same results when I pause outside of a function. Is this because the debugger creates its own block scopes at each line in the console? But if that's the case why would it work if not paused?
  
**Thoughts and Feelings:** Moving around every fifteen minutes is helping my back. I coded inside the rv for the full 2 hours to avoid eye strain.

**Link to Work:** [Pomodoro, working](https://github.com/dangerousdashie/pomodoro/tree/24069bad270b202418a47f77e97c026fdf154f56)

## Day 51
### R1D51 2/20/19
- **Pomodoro:**
   I worked on the buttons that add and remove input fields, so the user can customize their pomodoro intervals. I used bind() to bind this and now understand bind() better. But I ended up not needing it and removed it.
   
   I found out that even though functions should have access to sibling functions, you can't access a sibling function in the debugger unless it's somewhere in the inner function. The sibling function doesn't have to be called inside the function, you can just write the identifier of the function:
   ```javascript
   function brother(){
      console.log("I'm a sibling to sister()");
      debugger; //you CANNOT call sister() in the debugger, it will be undefined
   }
   function sister(){
      brother; //brother is referenced
      console.log("I can see my sibling, brother()");
      debugger; //you CAN call brother() in the debugger
   }
   ```
   I refactored some of my code. I made a makeElem() function that takes an element type (div, button, input, etc...), a class name, and optional inner html text and creates that element. In this project, most of my elements are dynamic, meaning they're created by the JavaScript code, so makeElem() will be useful. I added more elements like "add" and "remove" buttons, worked on the css and grid layout, and worked on event listeners for the 'add' and 'remove' buttons.
   
   ![screenshot](log_imgs/pomodoro_2_20.png)
   
   I ended today trying to add a document fragment but I didn't know the right syntax, so that's where I will start tomorrow.
   
**Thoughts and Feelings:** I took mandatory stretch breaks every 15 minutes which was really helpful. If I was sitting in a way that could cause kinks in my body, I had to change positions after each break. My screen strains my eyes outside even in the shade when the sun gets bright enough, so I have to move inside at around 10am. It would be great to get glasses that can fix this problem. 

I like working on a project from scratch. I'm implementing a lot of the concepts I learned in my tutorials. Putting these concepts to use helps me grasp them better.

**Link to Work:** [Pomodoro working](https://github.com/dangerousdashie/pomodoro/tree/24069bad270b202418a47f77e97c026fdf154f56)


## Day 50
### R1D50 2/19/20
I decided code for 2 hours, today, and forgo my code study hour. I took a break from [JavaScript30](https://javascript30.com) and decided to make a pomodoro timer app. 


- **Pomodoro:**
   I started by writing down on paper what I would need for my pomodoro timer app. I thought about the UI and I listed out different things I'd need.
   
   ![screenshot](log_imgs/pomodoro_sketch.jpg)
   
   Then I transfered my sketch to psuedo code.
   ![screenshot](log_imgs/Pomodoro_psuedo_code.png)
  
   Then I started **coding**.
   
   I learned that you cannot use arrow functions as constructors:
   
   **Works** as a Constructor, *regular function*:
    ```javascript
    var Pomodoro = function(el){
        this.el = document.getElementById(el);
    };
    ```
    
   **Doesn't Work** as a Constructor, *arrow function*:

     ```javascript
    var Pomodoro = (el)=>{
        this.el = document.getElementById(el);
    };
    ```
    
   I still have a lot to do.
   
**Thoughts and Feelings:** I got up at 6:55am today so I could get everything done before it got too hot. Yesterday, I was coding and sweating and I couldn't concentrate. Now, it's 11:47am and I've already finished exercising, two hours of coding, and I'm almost done with this log. I could have finished earlier if I had enough battery power, but I had to charge my battery a few times. By getting up early, I avoided coding during the hottest hours of the day.

I enjoyed working on my own app in place of my hour of studying and my hour of hands-on coding with [JavaScript30](https://javascript30.com). I think it was helpful to do a project from scratch again. I'll continue this project for a few days, and maybe throw in some studying and Javascript30.

**Link to Work:** [Pomodoro working file](https://github.com/dangerousdashie/pomodoro/tree/d4b732e462cc6429ce2f52295ee3a67c44fa5f5f)

## Day 49
### R1D49 2/18/19
- **Study:**
  
   Finished Vanilla JavaScript Propagation and Binding tutorial. 

- **Coding:**
 
   Finished Key Sequence detection and Slide in on Scroll from [JavaScript30](https://javascript30.com).
 
 **Thoughts and Feelings:** Today was difficult. It's so hot. I am sweating. I was coding outside but realized the lighting outside is too bright for my screen which caused eye strain. So I did the rest of my coding inside. But it's even hotter inside the RV. It's 89 outside, so probably 95 inside. Idk. Be grateful for AC when you have it. We can run AC if we run the generator. Maybe next time it's this hot we'll just do that. 
 
 I didn't drink my decaf coffee today. I decided to quit. Even decaf could have enough caffeine to raise corticol levels. I feel like it was increasing my muscle tension. I was getting tension in my back, neck, and face muscles while coding. If I want to be a successful coder, my body needs to work well. 
 
The Vanilla JavaScript Propagation and Binding tutorial wasn't taught very well. It was confusing becuase the instructor didn't really show us was we were going to make. She just did a lot of coding without compartmentalizing or demonstrating what sections of the code were for. So I decided to speed through it and then go back and try to make what the instructor made. I havn't gone back to make it yet. I think that will really help me with code organization becasue the instructor was very organized with her code.
   
**Link to Work:** [Key Sequence Detection](https://github.com/dangerousdashie/JavaScript30/blob/ae9bff27afcf15004dc76de554a49dc0cff7b933/12%20-%20Key%20Sequence%20Detection/index-START.html), [Slide in on Scroll](https://github.com/dangerousdashie/JavaScript30/blob/ae9bff27afcf15004dc76de554a49dc0cff7b933/13%20-%20Slide%20in%20on%20Scroll/index-dashie.html)

## Day 48
### R1D48 2/17/19

 - **Study:**
  
   Continued Vanilla JavaScript Propagation and Binding tutorial.

   
 - **Coding:**
    
    Finished the extra challenge in 'Custom HTML5 Video Player' from [JavaScript30](https://javascript30.com).
    
    Started 'Key Sequence Detection' from [JavaScript30](https://javascript30.com). I got stuck on using the splice array. I'm trying to understand it better.
    
    **Thoughts and Feelings:** I think I need to give up coffee. I only drink decaf but I think it's still too much caffiene. I think it's raising my cortisol levels and causing my back and headaches while coding.

**Link to Work:** [Custom HTML5 Video Player](https://github.com/dangerousdashie/JavaScript30/blob/c117c40e22789e047edc605b5dea6543e02c1ce9/11%20-%20Custom%20Video%20Player/script-dashie.js), [Key Sequence Detection unfinished](https://github.com/dangerousdashie/JavaScript30/blob/c117c40e22789e047edc605b5dea6543e02c1ce9/12%20-%20Key%20Sequence%20Detection/index-START.html)
## Day 47
### R1D47 2/16/19

 - **Study:**
  
   Finished JavaScript Classes tutorial. Started Vanilla JavaScript Propagation and Binding tutorial.
   
 - **Coding:**
    
    I started the 'Custom HTML5 Video Player' challenge from [JavaScript30](https://javascript30.com). I left off where Wes said to try to add a full screen button on your own. I did pretty well on this project.
    
**Thoughts and Feelings:** I started implementing mandatory 20:20:20 breaks. That means every 20 minutes I look at something 20 feet away for 20 seconds. It's supposed to prevent eye strain. I also do somme stretching to help with my back. I think it's helping.

**Link to Work:** [Custom HTML5 Video Player, nearly finished](https://github.com/dangerousdashie/JavaScript30/tree/1c6d42912a99418d24c9f3c17b573f49b7c9b746/11%20-%20Custom%20Video%20Player)

## Day 46
### R1D46 2/15/19
 - **Study:**
  
   Finished JavaScript Events tutorial! I really enjoyed this tutorial. I want to recreate the last project, which was a drag and drop game for a building a snowman. I want to make the same game but for dressing up someone I know. 
   
   Started JavaScript classes tutorial.
   
 - **Coding:**
 
    Went over Wes's solution for the 'Hold Shift' project. I did it wrong because I thought you were supposed to make it work when you press and hold shift while pressing BOTH checkboxes, but you were supposed to make it work only when pressing shift while holding the last button. I redid it while watching the video.
    
    I started the 'Custom HTML5 Video Player' challenge. I still need to finish it.

**Link to Work:** [Hold Shift Follow Along](https://github.com/dangerousdashie/JavaScript30/blob/064d917d79b0af7455a48cf066584fc11f7e0326/10%20-%20Hold%20Shift%20and%20Check%20Checkboxes/index-follow-along.html), [Custom HTML5 Video Player Unfinished](https://github.com/dangerousdashie/JavaScript30/blob/064d917d79b0af7455a48cf066584fc11f7e0326/10%20-%20Hold%20Shift%20and%20Check%20Checkboxes/index-follow-along.html)

## Day 45
### R1D45 2/14/19

 - **Study:** Continued Javascript Events tutorial. Almost done! I got to the last video and was trying to finish but I realized my back was sore and I had to take a break. So I decided to be done with my tutorial for the day. This tutorial has been really fun because events are so action-based and interactive. 

   I spent a while trying to figure out how to traverse through the highlighted words when I generate a snippet in emmet. But I couldn't find out anything.  
   
   For example, if I start typing here, it will automatically start writing over the first selection, `device width`. But what if I want to skip over to the last highlighted word, `Document` so I can add a title? Is there a way to skip to here on the keyboard?  
 
   ![ScreenShot](log_imgs/emmet.png)
 
 
 
 - **Coding:** I started and completed the [JavaScript30](https://javascript30.com) 'Hold Shift' challenge. I still have to watch how Wes solved it. 

**Link to Work:** [Hold Shift](https://github.com/dangerousdashie/JavaScript30/blob/725e933614749eecc91a062a1a98bdfa1dff8659/10%20-%20Hold%20Shift%20and%20Check%20Checkboxes/index-dashie.html)
## Day 44
### R1D44 2/13/19
1. Continued Javascript Events tutorial on lynda.

2. The [JavaScript30](https://javascript30.com) video today was short and it wasn't a challenge so much as a tutorial. So I had time to do some of the [CSS Grid](https://cssgrid.io/) tutorials. But those also weren't challenges. So I started the [100dayscss](https://100dayscss.com/) challenge for today. It was so interactive, I couldn't figure out how to do it purely in css. So I looked at how some others solved it. Most people used javascript. Not cool guys! But I found someone who did it with just css, I only looked at it a little bit but it looked like they styled an input checkbox that changed when you toggle it. It was really hacky. Of course, I could have misunderstood what they did. I'm not sure what the answer is. 

![ScreenShot](log_imgs/css2_13_19%20copy.gif)

**Thoughts and Feelings:** Today I offered to help anyone on twitter with coding. No one took me up on it but it felt good to offer. I'm trying to implement more 'giving' behaviors because I'm reading the book *Give or Take*. It says that when we try to help the community's progress as a whole, we all thrive.

## Day 43
### R1D43 2/12/19
1. Continued the JavaScript Events tutorial on Lynda.com. I'm finding all the things you can do with events interesting and fun. I found this [cheat sheet](https://code.visualstudio.com/shortcuts/keyboard-shortcuts-macos.pdf) for awesome Visual Studio Code short cuts for mac. 

2. Completed the **HTML5 Canvas API** challenge from [JavaScript30](https://javascript30.com). This project was cool. It makes me want to play with the Canvas API more.

![ScreenShot](log_imgs/canvas2_12_19.gif)

3. Started Wes Bos's [CSS Grid](https://cssgrid.io/) tutorials.

**Thoughts and Feelings:** Great day. I'm excited to get more practice with grid.

**Link to Work:** [HTML5 Canvas API](https://github.com/dangerousdashie/JavaScript30/blob/5bb2ce7583922cca91e9d949f1fc2577f3f473e6/08%20-%20Fun%20with%20HTML5%20Canvas/index-START.html)

## Day 42
### R1D42 2/11/19
1. I continued the JavaScript Events tutorial on Lynda.com.

2. I started and completed the **Array Cardio Day 2** challenge from [JavaScript30](https://javascript30.com). 

3. I started a project that tracks my exercise and coding streaks called **Dashie Tracker**. 
![ScreenShot](log_imgs/dashie_tracker2_11.png)

**Thoughts and Feelings:** I really felt like I could have coded for longer. I have a few projects I want to try. But it's cloudy so our power is limited today, since we run on solar.

**Link to Work:** [Array Cardio Day 2](https://github.com/dangerousdashie/JavaScript30/blob/d715047a0af3774b87090a1d3fbbb1fce307ef4b/07%20-%20Array%20Cardio%20Day%202/index-dashie.html), [Dashie Tracker](https://github.com/dangerousdashie/dashie-tracker/tree/06d956b98ab870d6f536900a482fc438d19e2ff0)
## Day 41
### R1D41 2/10/19
Continued the JavaScript Events tutorial on Lynda. I learned some tricks for selecting in Visual Studio Code:

- **Cursor on Multiple Lines:** Put the cursor somewhere, then `opt`+`cmd`+`down or up arrow` 

- **Once Your Cursor is on Multiple Lines, Select to the End:** `shift`+`right arrow` 

- **Move a Line of Code up/down:** `opt`+`down or up arrow`

- **Copy a Line of Code up/down:** `opt`+`shift`+`down or up arrow`

- **Start an Anchor Tag:** `a` then `tab`

You can even use these short cuts to copy from multiply lines and then paste into multiple lines.


Watched Wes's solution and redid [JavaScript30](https://javascript30.com) Type Ahead challenge. I was struggling for a while because I had written a return statement like this:
        
        return 
        `<li>
        <span class="name">${cityName}, ${stateName}</span>
        <span class="population">${numberWithCommas(place.population)}</span>
        </li>`;
        
But since javascript sometimes puts in semicolons by itself, that actually works like this: 
        
        return; 
        `<li>
        <span class="name">${cityName}, ${stateName}</span>
        <span class="population">${numberWithCommas(place.population)}</span>
        </li>`;
        
So you need to start the string literal on the return line:

        return ` 
        <li>
        <span class="name">${cityName}, ${stateName}</span>
        <span class="population">${numberWithCommas(place.population)}</span>
        </li>`;
        
I also played around with my formatting on this log on this entry. I really like how [James Priest](https://github.com/james-priest/100-days-of-code-log-r4) does his. He also has it on [his own website](https://james-priest.github.io/100-days-of-code-log-r4/), with some styles applied. I'm not sure how he has his log connected to this site. I just looked and he talks about how he did it on [day 11 and 10](https://james-priest.github.io/100-days-of-code-log/#11-windows-10-ubuntu-1604-jekyll--github-pages) of his first round.

**Thoughts and Feelings:** I felt like coding for longer, but I ran out of power. I could charge up but I think I will save power. Maybe I will do more later.

**Link to Work:** [Followed Wes, Type Ahead](https://github.com/dangerousdashie/JavaScript30/blob/3075334ff435ed9de90e5947e6e59205bfe37e8d/06%20-%20Type%20Ahead/index-follow-along.html)
### R1D40 2/9/18
Finished JavaScript Patterns tutorial on lynda. Started JavaScript Events tutorial on Lynda.

Finished [JavaScript30](https://javascript30.com) Type Ahead challenge. I still have to finish watching how Wes did it.

**Thoughts and Feelings:** Took a lot of breaks and felt pretty good today.

**Link to Work:** [Type Ahead](https://github.com/dangerousdashie/JavaScript30/blob/ef173ff62cd4fb1ad8f3ec86dd2c97f318e5a5c2/06%20-%20Type%20Ahead/index-dashie.html)
### R1D39 2/8/19
Continued lynda.com javascript patterns tutorial. 

Finished [JavaScript30](https://javascript30.com) Flex Panel Gallery. I got stuck because I forgot you can use `this` and was only using the the `event.target` in the event handler. Which wasn't good because that sometimes gave me an element nested in `this`.


**Link to Work:** [Flex Panel Gallery](https://github.com/dangerousdashie/JavaScript30/blob/d556e026cf3349baf171323da85f5a7ad8aa5e51/05%20-%20Flex%20Panel%20Gallery/index-dashie.html)
### R1D38 2/7/19
Continued lynda.com javascript patterns tutorial.
Finished [JavaScript30](https://javascript30.com) array cardio day 1.

**Thoughts and Feelings:** I want to practice array methods more. I think reduce() is interesting. I'm kind of tired today. I didn't go on social media all day which is nice but I still ended the day late probably because I spoke to other campers, woke up and read for an hour, and did laundry. Tomorrow I need to finish early so I have time to work on the RV heat sink before our friend's yoga photoshoot at 4 that we volunteered to help with.

**Link to Work:** [Array Cardio Day 1](https://github.com/dangerousdashie/JavaScript30/blob/fcebbdf8cf2244a8de0bba72dfc404f19bfd11c8/04%20-%20Array%20Cardio%20Day%201/index-dashie.html)
### R1D37 2/6/19
Continued lynda.com javascript patterns tutorial. 

Started [JavaScript30](https://javascript30.com) array cardio day.

**Thoughts and Feelings:** Shlomo was talking on speaker phone and I was paying attention to that while coding. So I was probably distracted. But it was an easier assignment today. I probably could have finished it if I wasn't listening to Shlo. But it's ok, I wanted to take it easier today because I have a tense forhead muscle and back muscles. It might be from dehydration. So today I drank a lot. After my tutorial I did Muse meditdation before coding.

**Link to Work:** [Array Cardio Day 1, unfinished](https://github.com/dangerousdashie/JavaScript30/blob/2def97f1c845944c08e9705839bbf35c24639df4/03%20-%20CSS%20Variables/index-dashie.html)

### R1D36 2/5/19
I finished the lynda tutorial for JavaScript Functions. I started JavaScript Patterns.

I started and finished the [JavaScript30](https://javascript30.com) CSS Variables project. I got stuck for a while because I was using the addEventListener() function but I was passing in console.log("test") where the callback function should be. I thought it would work the same but it doesn't. Probably because console.log() is being invoked where as the callback function in the addEventListener() function shouldn't be written as invoked: callback() *(invoked)* vs callback *(not invoked)*. Tomorrow I'll watch the rest of the tutorial to see how Wes did it. I saw some of how he did it as I was figuring it out.

**Thoughts and Feelings:** I'm excited to learn about JavaScript patterns. I feel good that I started and finished a [JavaScript30](https://javascript30.com) challenge.

**Link to Work:** [CSS Variables](https://github.com/dangerousdashie/JavaScript30/blob/2f487aab001be65d34a2197f5542541dce448796/03%20-%20CSS%20Variables/index-dashie.html)
### R1D35 - 2/4/19
Started watching UC Berkeley's lectures for [CS 61A](https://www.youtube.com/watch?v=Tkciq5IQVp0). I continued Javascript Functions on lynda but I'm noticing how out of date some concepts are because it's from 2013 before ES6.

I finished the clock from [JavaScript30](https://javascript30.com). It took a while because I was having trouble removing the hand transition for the point where the hand goes backwards. Eventually, I got it to work but I'm not really sure why it was working before.

**Thoughts and Feelins:** I can't believe there are so many video lectures of college courses. Why are so many people paying for these classes when they're free? I started reading this text book last night called [Computer Organization and Design](https://github.com/hamadkh/MIPS-iVerilog/blob/master/Morgan.Kaufmann.Computer.Organization.And.Design.5th.Edition.Oct.2013.ISBN.0124077269.pdf). It started off ok, sort of easy to understand. But then I was doing the exercises for each section and they were so bad. The questions talked about things that the corresponding section didn't talk about. It used really specific terms that the book didn't cover yet. As I write this I'm wondering if you had to read the rest of the chapter to understand the questions and not just the section that the exercises referenced. Afterall, the questions were at the end of the chapter. I was reading them before I finished the chapter. maybe I should give it a second try? Otherwise, these text books are just so dry and poorly written I don't know if it's worth my time. Especially when there are so many quality videos, lectures, and blogs to explain these concepts. 

**Link To Work:**[Finished Clock](https://github.com/dangerousdashie/JavaScript30/blob/fd0ea5b4279f7c207ed50aa0bbcb5bff98b17ed6/02%20-%20JS%20and%20CSS%20Clock/index-dashie.html)

### R1D34 - 2/3/19
I continued the Javascript Functions tutorial on lynda.com.

I posted my issue from yesterday on stackoverflow [here](https://stackoverflow.com/questions/54506846/chrome-devtools-htmldivelement-style-property-set-before-function-called?noredirect=1#comment95817550_54506846).

Apperently this is happening because 
>"Devtools console contains a **pointer** to the actual element in all three messages which is why it displays identically."

Now, what this means exactly? I'm not sure. I can guess roughly what that means. The same person suggested,

>"You might want to log out just the property itself." 

Logging `div.style.border` worked as expected. I'd like to understand what a **pointer** is.

**Thoughts and Feelings:** I meditated for 15 minutes inbetween my tutorial and my coding. I used my Muse headband. I think that was really helpful. I was able to code for a while *(though I spent my coding time mostly posting on stackoverflow, debbuggin, and trying to understand what was going on with my issue.)* I reorganized this github log so that the last day is at the top. Much better!

Whenever I get answers on stackoverflow I feel like the answers were written in a snarky tone. Maybe it's all in my head. But if they really are being snarky, then I should recognize this: ***the newbies who push through the snark and ask questions anyways are the ones who succeed*** So that's what I'm going to do. *Be grateful for every answer even if the person is being snarky.* Ignore the snark. Take the answer.

---

### R1D33 - 2/2/19
It took me a while to figure out the bug in my lynda tutorial challenge for Javascript functions. The reason it said innerHTML is not a function is because it isn't. It's a property! Duh! I was trying to use it like a function. 

  What I was doing:

  `elems[0].innerHTML("hello world");`

  But it should have been:

  `elems[0].innerHTML = "hello world";`

I have gotten so used to jQuery which has an `html()` function. So I was trying to use the `.innerHTML` property like a function.

I also learned that if you declare a variable and then use += on it before intializing the variable, "undefined" is included in the beginning. You have to set the variable to an emtpy string first. More on that [here](https://stackoverflow.com/questions/29198651/first-element-in-object-is-undefined-using-operator).

---

I worked on the clock project from [JavaScript30](https://javascript30.com) and ran into some weird behavior. I isolated what's confusing me into a separate file [here](https://github.com/dangerousdashie/JavaScript30/blob/21c938820efa70c13773f0b2676a61e93d971a19/02%20-%20JS%20and%20CSS%20Clock/test_node_behavior.html). 

It's a script that, when `addBorder()` is called, adds a border to the div. But when I `console.log()` the div *before* `addBorder()` is called, you can see that for some reason, the style that is added in `addBorder()` is already applied.

![ScreenShot](log_imgs/R1D33/node.png)

All three times that I `console.log()` the div, it has the border applied. Whether the log was called before `addBorder()`, after `addBorder()` was called but before the border property is set, or after the property is set. This is not what I expect. I would it expect it to look like [this](https://github.com/dangerousdashie/JavaScript30/blob/21c938820efa70c13773f0b2676a61e93d971a19/02%20-%20JS%20and%20CSS%20Clock/test_num_behavior.html) script runs, which is pretty much the same script except the element in question is no longer a div, but a number and the number is being multiplied by 10. In the log you can see the number remains 3 until after it is multiplied by 10 inside the `multiplyBy10()` function.

![ScreenShot](log_imgs/R1D33/num.png)

And oddly enough, the script with the div will log a different result if I keep everything the same but I step through the whole script in the debugger slowly. In that case it still doesn't give the expected result, because it shows no border property in the last log.

![ScreenShot](log_imgs/R1D33/step.png)

What is going on? Is this a scope issue that only effects certain data types?

**Thoughts and Feelings:** Since I spent so much time confusing a property with a function, it's probably really ingrained in my brain now to check that mistake in the future. This is a pattern I noticed. I'm able to pick up on bugs so much quicker than I used to. Every bug that tooks me 45 minutes to find trains my brain to look for that bug quickly later on. So then next time I spend an hour on a tiny bug, I'll be grateful. My mind will have **"downloaded"** the ability to quickly scan for that bug in the future. An hour one day saves me many hours in the future. It's truly a gift you can't buy. Treasure those bugs!

This is what Shawn Achor means in *The Happiness Advantage* when he talks about *"falling up."* Adversity isn't just something you overcome, it's something that measurably makes you better than before. Feed your brain with challlenges, adversity, & failure! You need these to succeed.

### R1D32 - 2/1/19
I did more of the functions tutorial on lynda. I started the challenge for the 2nd chapter but got stuck because it's saying innerHTML is not a function for the element I'm targeting. 

I continued the clock project from [JavaScript30](https://javascript30.com). It's almost done but the second hand goes backwards when it jumps from 59 to 0. 

I plan to read more of [*Structure and Interpretation of Computer Programs*](https://github.com/sarabander/sicp) later today.

**Thoughts and Feelings:** Took a break for a snack after 1hr and 20 ish minutes or my tutorial and coding and I felt good. I also took a pee break 34 minutes into my tutorial. But I really have to pee now and I'm holding it so I can finished posting before my computer dies.

**Link To Work:** [Continued Clock](https://github.com/dangerousdashie/JavaScript30/blob/c192da7e4ca4874b7ee226c453d34bef11828e25/02%20-%20JS%20and%20CSS%20Clock/index-dashie.html)

### R1D31 - 1/31/19
I watched Wes Bos's solution to the Drum Kit challenge. It was really different than mine. Things I learned:
1. There is a `keycode` associated with every key event. ex. the event `.keycode` property for **a** is **65**.
2. There is an event listener called transitionend that runs when a css transition has ended.
3. Calling `.play()` on an audio element that is currently playing won't do anything. To override this, you can set the audio element's `currentTime` property to 0 and then call `.play()` to play it from the beginning.

I started the clock project from [JavaScript30](https://javascript30.com). It's getting easier to see how to organize my code. It's becoming more natural to me to see where my code is headed and how I'll need to organize it. I think all these JavaScript lynda courses might be helping with that.

I studied JavaScript functions on Lynda.com. I also started reading a textbook called [*Structure and Interpretation of Computer Programs*](https://web.mit.edu/alexmv/6.037/sicp.pdf). The book was on [this list](https://teachyourselfcs.com/) on [teachyourselfcs.com](https://teachyourselfcs.com/) of concepts to master to understand computer science. Every book on that list is available for free. I got them all on pdf now. I want to try to find an e-book version of them all because that will make note taking easier. 

**Update:** I found *Structure and Interpretation of Computer Programs* in an ebook format [here](https://github.com/sarabander/sicp). To find it, scroll down to the README.md section and click the link where it says
**"In EPUB3 format."**

**Thoughts and Feelings:** I took a break after 1 hour and 45 minutes of tutorial (1h) and coding (45m). I sat in the sun and then came inside and had a cucumber with almond butter and peach seltzer. When I came back to coding I felt so much more refreshed and worked even longer. My mind was so clear. I think I should implement more breaks and snacks. I don't know if it was the snack itself that boosted my energy or just the fact that snacks make me happy. Maybe anything that makes me relaxed and happy would be good during a break. 

**Link To Work:** [Started Clock](https://github.com/dangerousdashie/JavaScript30/blob/48f36c908811f361201f9aa36d16d84360357914/02%20-%20JS%20and%20CSS%20Clock/index-dashie.html)

### R1D30 - 1/30/19
I finished my closures tutorial. At the end it said "if you want to learn more about vanilla javascript" and I was like "what are they talking about? I didn't learn anything about vanilla javascript, how could I learn MORE? And I have no clue what it is." I had seen Vanilla JS mentioned and figured it was just another framework. I finally looked into it. It just means coding javascript without any libraries. Great to know, because that is exactly what I want to learn so I really understand the basics of javascript. Now I can stop ignoring all the Vanilla JS tutorials on lynda.com. I organized my notes and reviewed git.

I found a set of [tutorials/projects](https://javascript30.com/) in vanilla javascript by Wes Bos. I worked on the first project today and finished it. I didn't yet look at Wes's solution, but mine works.

**Thoughts and Feelings:** I feel so much more optimistic today. I'm reading The Happiness Advantage and it shows how important it is to be happy and optimistic. It's essential to reducing burnout and improving productivity. The last few days I felt a burnt out. I was met with challenges that I didn't solve and felt bad about my skill set. But I feel optimistic again that I'm on the right path and will keep learning and growing. Partly because I took on a challenge today that I succeeded in, but also because I worked on my mindset a bit. Next time I feel overwhelmed in my studies I can take a step back and try to work on a challenge that is closer to my skillset or study the basics. I will eventually get back to the harder stuff. A challenge that I can't solve is just an indication that I need to learn more. It's a gift.

**Link To Work:** [Drum kit](https://github.com/dangerousdashie/JavaScript30/blob/master/01%20-%20JavaScript%20Drum%20Kit/index-dashie.html)

### R1D29 - 1/29/19
I worked more on trying to figure out the API ordeal for meetup. I posted on stackoverflow and got some answers that I don't understand but will look into it late. I think I might take a break from this project. My friend Benjy said you can't use the meetup API without the OAuth and to do that you need a website with an SSL so I can't really use it. But the other answers suggested I might be able to use the API with just the key, so I'll see. I feel like I havn't dont a lot of coding, just investigating this issue. So maybe I'll try to do something easier for now so I can actually code.

For my tutorial I did closures on lynda.com. I find this concept a little confusing still.

**Thoughts and Feelings:** I coded in the public bathroom today to fill up on battery power so my legs are soo numb right now.

### R1D28 - 1/28/19
I did more of my closures tutorial. 

I worked more on my meetup API project. Apparently 3rd party apps can't use API keys. They have to use OAuth. I've never done this and I'm not even sure what it means or whether I'm a 3rd party app but I must be because CORS is not allowing me to use my API key with my app. This is the info for [meetup OAuth registration.](https://secure.meetup.com/meetup_api/oauth_consumers/create/) I'll have to look more into this tomorrow. Right now, I must pee!

**Thoughts and Feelings:** Yesterday, I was feeling like I hit a wall, a bit frustrated. I didn't know how to search for an exact phrase using meetups API. I felt really lost because I didn't know if it was possible and if it was possible I didn't know whether it was a meetup specific thing or if the answer was applicable to all api's. I didn't know where to turn. I havn't had much luck on stackoverflow. But then I realized I didn't need to do an exact search. Of course, I can just extract out the data I need in my code! Duh! I think there is no way to do it through meetups api. I need to not feel so frustrated. I need to remember that hitting a wall is just making a discovery. Sometimes you discover that things can't be done as you expected. Or you discover you don't know how to do something. This is the wall. But the answer is just around the corner on the other side of the wall.

### R1D27 - 1/27/19
I continued my tutorial on closures. I started working with meetups API but I'm kind of lost. I want to search for groups or events that mention "lucid dream"- exactly, but I can't figure out how to do that. When I add the text parameter, text=lucid+dream, it shows me results that use the word lucid by itself or dream by itself.

**Thoughts and Feelings:** It's raining and gross and I'm feeling weird. How can I find out how to use this API?

### R1D26 - 1/26/19
I finished the javavascript scope tutorial and started a javascript closures tutorial on lynda.com.

In my clock project, I have a working clock, though it's not designed cute and the code should be cleaned up. I used [this](https://www.w3schools.com/graphics/canvas_clock.asp) tutorial as reference when I ran into trouble. 

Now I'm trying to make the clock face change colors depending on the UV levels outside using the [open weather API](https://openweathermap.org/appid). I'm stopping for right now because I need to charge my laptop. I might come back to it later today. I think I might also try doing the clock in svg instead of canvas. Canvas uses bitmap so the lines aren't as smooth as the vector would be with svg. Here's the [clock](https://github.com/dangerousdashie/javascript-clock/tree/5ab15956c847289712477081e51489dd8390b669) so far.

I worked more on the clock and now it sets the background of the clock to a color value on a blue to grey scale depending on the UV value grabbed from the open weather API for Punta Gorda, Florida. That's where I am at the moment. Next I will try to get it to work for any location. 

**Link To Work:**[clock latest commit, UV API](https://github.com/dangerousdashie/javascript-clock/tree/de5c6c32ce617f927b90a83d61054fc670e221a0)

### R1D25 - 1/25/19
I started a tutorial on javascript scope. I think I know most of it so far. I don't know what block scope is and that's next. 

I got further along in my javascript clock project. Right now it displays the right time on a canvas circle with two lines for the minute and hour hands. However it doesn't update with the time, you have to refresh it. So next I need to figure our how to update it. I should also add a second hand. My code is probably too redundant. 

**Link To Work:** [clock](https://github.com/dangerousdashie/javascript-clock/tree/135fa05ff5aa68a306bc7baa0b92d1acd4dfb688)

### R1D24 - 1/24/19
I fixed my git issue that I had on day 22. I organized my Readme file for my quiz project. I explored github. I want to beable to use it better and understand it more. I'd like to be more connected with the online programming community. I finished my JavaScript prototypes tutorial on lynda and practiced using the class keywork on [code pen](https://codepen.io/Dashiee/pen/KJdZGa?editors=0011). 

I started working on a JavaScript clock.

**Link To Work:** [Quiz Template latest commit](https://github.com/dangerousdashie/jQuery-quiz-template/tree/7f974bf75d16f92de02048bad89e25e57f89a604)

### R1D23 - 1/23/18, completed 1/23/18
I worked mostly on debugging. I'm having an issue with debugging that I documented [here](https://stackoverflow.com/questions/54333252/pause-on-execution-pausing-on-vm-file). No one has helped me on stack overflow with my issue so I'm wondering where to look next. I also spent a little bit of time on CSS. I studied Javascript prototypes yesterday.

I know that I am going to run into a lot of frustrating problems that seem like dead ends; no one answering me on stackoverflow, trying to get approved for API's and being ignored. These moments are gifts because I know that finding unique solutions to these setbacks will set me apart from others. This is where most people give up but I will keep going. Failure comes right before success.

### R1D22 - 1/22/18
I finished my quiz but I didn't finish the read me file. It's a quiz template others can use to make a quiz. You just need to change the JSON data file to contain your quiz info. I want to clean it up a bit and add a nice readme. Maybe I can turn the project into a tutorial. 

I have an issue with git with this project. I added a readme file and changed the local files before merging them. So now I can't push to github. I don't remember what to do to merge them properly. I need to look it up. 

**Thoughts and Feelings:** It's my birthday and I'm 28.

**Link To Work:** [Quiz Template, read me not finished](https://github.com/dangerousdashie/jQuery-quiz-template);

### R1D21 - 1/21/18
I completed day 21 yesterday but I am logging it today. I worked on the quiz. I had trouble getting the function that displays the results to work. 

**Thoughts and Feelings:** I probably could have figured out the results function. But my MIL kept distracting me. She kept trying to talk to me. Specifically, she was trying to find out what jewelry I wanted to inheret from her. If I have to be distracted, that's not a bad way. But before that, I felt really confident in what I was doing. 

I need to make sure to reduce distractions. But I was afraid of being rude. But I just have to ask to not be disturbed nicely. Maybe, "Can you bring this back up to me in like 30 minutes. I'm trying to give my full attention to both you and my coding and I'm doing a bad job at both."

Besides that, I felt really confident in what I was doing. I didn't have to reference much code. I could do it mostly from memory. Having the logic organized in sections with comments helped me understand what I needed to do next. 

**Link To Work:** [Second Commit (unfinished) for Quiz](https://github.com/dangerousdashie/first-quiz-project/commit/f2110b94c3e6ae1065bed2b8c89ee047595090ec)

### R1D20 - 1/20/19
Coding: I started to make a quiz with JavaScript, JSON, CSS, HTML, and jQuery. I organized my code by commented sections like `//-----click event for answer` to be more organized with my code. I used what I learned about the DevTools Debugger to solve issues.

Tutorial: I continued my tutorial on JavaScript prototypes on lynda.com. I used the dubugger to figure out problems in my code when I was following along. I had a problem with the debugger that I posted about [here](https://stackoverflow.com/questions/54279206/pause-script-execution-in-chrome-devtools-not-working-as-expected?noredirect=1#comment95380257_54279206).

**Link To Work:** [First Commit (unfinished) for Quiz](https://github.com/dangerousdashie/first-quiz-project/commit/59c60f4d67f991118d99184796ee9b005b458196)

### R1D19 - 1/19/19
I drew out a few layouts I wanted to make and then used css grid to make them. I was having trouble because my footer would align to the top of the grid row. This [tutorial](https://dev.to/niorad/keeping-the-footer-at-the-bottom-with-css-grid-15mf) solved my problem. I wanted to try to see if I could create a dynamic grid-template-row value so that the recommendation in the tutorial (grid-template-row: auto 1fr auto;) works for more than 3 rows. I can manually do this with media queries, but I thought what if I don't know how many rows there will be? It would be great if I can have the grid fit to the header and footer by always having the first and last rows set to auto. But always have every row inbetween be 1fr no matter how many rows. I don't think there's a way to do this without some plugins. I couldn't find a property in jQuery or the DOM that could do this. I think you can maybe do it with CSS variables but I don't know how. I just realized I can maybe do what I want with grid-auto-rows. No nevermind, I can't. 

I realized using margin on my grid throws things off because the box sizing is by the border and margin is outside of the border. So You have to do padding instead. 

My twitter app still wasn't approved. For my tutorial I did debugging javascript and started javascript prototypes on lynda. I also reaplied for a My Fitness Pal API key.

**Thoughts and Feelings:** I'm feeling a lot more comfortable with grid. I'd like to start focusing on getting my javascript experience again and move away from just css layouts since I'm more comfortaable with those. I need to think about some project ideas that will apply to what I'm learning while I'm waiting to get my twitter app approved. 

**Link To Work:** [Grid Layout Example](https://github.com/dangerousdashie/grid-layout-header-footer-example)

### R1D18 - 1/18/19
I am following this [tutorial](https://hackernoon.com/create-a-simple-twitter-bot-with-node-js-5b14eb006c08) to create a twitter bot but I have to wait to get my projecf approved. They don't talk about that in the tutorial because I think it's a new twitter policy. I also worked on recreating layout9 on gridbyexample.com. For my tutorial I did javascript debugging on lynda.

### R1D17 - 1/17/19
I worked on css grid and responsive layout today. I found a great site that has a bunch of sample layouts. I tried to copy one of them and made a repo documenting my process. For my hour of tutorial I did lynda's javascript debugging tutorial.

**Thoughts/Feelings:** I had a lot of energy today during my coding and my tutorial. I was very motivated. Where as, the past few days, I'd just been going through the motions and not enjoying myself, today I was focused, attentive, and energized. I think these things helped: Getting pumped before my coding and tutorial by reading about coding and learning. I also read through some tutorials that sounded interesting to me which got me excited about coding them in the future. Instead of setting a timer to 60 minutes, I set a stopwatch and just let it run. So I wasn't thinking about the time as much. I normally wonder, when is the timer going to go off? I was instead focused on the task. I took some breaks to re-energize. For my coding, I gave myself an actual goal: finish a layout. Sometimes I make my goal too broad ex. practice layouts. A specific goal keeps me interested because I have something to attain. For my tutorial, I connected my overall goals with learning debugging. I thought about how learing to debug right now is going to make all my coding easier. This motivated me.

**Link To Work:** [Layout Copycat project](https://github.com/dangerousdashie/grid_by_example_layout8_copycat)

### R1D16 - 1/16/19
I went through grid garden. Then I tried making a responsive 12 column grid with media queries. I realized I understand less than I thought.

**Thoughts/Feelings:** I need to work on getting more pumped while coding like I do with exercise. I need to implement "goal setting to the now" breaks to reinvigorate my brain. I think I may need more of a concrete goal with coding. Today, I was just like, "let's practice responsive design." But maybe I need to plan more what I'm going to try to make to have a goal I can accomplish.

**Link To Work:** [Responsive Grid Practice](https://github.com/dangerousdashie/responsive_design_practice/tree/072164083ab3b3ffcd29b06cbe434aac512edc3f)

### R1D15 - 1/16/19
I completed day 15 yesterday but didn't update my log.  I finished the responsive layout challenge from the responsive layout tutorial on lynda. i had to look at what the teacher did. I had way over complicated what I was supposed to do. I finished the Responsive Layout tutorial and the jQuery tutorial.

**Link To Work:** [Lynda Flexbox Challenge](https://github.com/dangerousdashie/lynda_flexbox_challenge/tree/894e4398aaea4168ebbfe295b73028ea4d69e2bd)

### R1D14 - 1/14/19
I worked on flexbox. The tutorial on lynda I'm doing gave me a challnge so that's what I worked on. I am having an issue with the header in the media query. It's doing somethings I don't expect it to do. It must have something to do with the css that it's inheriting, from before the media query. I wonder the best way to debug it. Maybe there's a course on debugging css. I use the debugger but maybe I can do a better job.

**Link To Work:** [Lynda Flexbox Challenge](https://github.com/dangerousdashie/lynda_flexbox_challenge/tree/f65a6daea1e88da7f5a38d86a1ec48ae98e33850)

### R1D13 - 1/13/19
I figured out my bug from yesterday. I was trying to decide a uri but i had never encoded it. I  dont totally ubderstand  why you have to do it that way. 

### R1D12 - 1/12/19
I am trying to grab the parameter value from the uri so I can append the poduct name from the uri to the page. It's not working and I'm not sure why but I think it may be a mistake it how I formed the uri to begin width. I'm going to compare what I did to what the teacher did. 

**Thoughts/Feelings:** Annoyed that I spent a whole hour hardly accomplishing anything. But I think every experience I have is good in some way.

### R1D11 - 1/11/19
I'm reading this book called The One Thing. The book talked about "Goal Setting to the Now" which helps you take a long-term view of your goals and narrow it down to what you need to do right now to move towards that goal. So I used the technique to figure out how I need to spend my coding time. I decided that I need to really practice my javascript. So today I looked at some of the other solutions on the katas I've done. I looked at the methods that people used and read the documentation on them. I tried them out myself. Even just reading the documentation is helpful because I'm getting exposed to terminalogy. The more I read the documentation the more I'm able to understand new information I read in the documentation. I want to do some more coding later, I feel inspired to learn more.

### R1D10 - 1/10/19
I looked at some of the other katas. Then I practiced jQuery. I made a project that involved my friends faces. I want to make it more fun. I did more jquery tutorial outside of coding.

**Link To Work:** [Friends Faces](https://github.com/dangerousdashie/friends_faces/tree/dc5ba4b7514dac337f300cb71ebcca6f05f93eda)

### R1D9 - 1/9/19
  I finished the [Number of trailing zeros of N!](https://www.codewars.com/kata/52f787eb172a8b4ae1000a34/train/javascript) kata. I used the debugger and jasmine unit tests. My solution was so unnecessarily verbose. It had an entire step that was unnecessary but that was more due to the fact that I misunderstood the equation to finding the trailing 0's for factorials than my ability to write clean code. Which is why I'm not a fan of these kata's that make you learn non-programing ideas. Uchhh! Maybe it will come in handy in trivia one day? But even still my code was verbose because I refactored it after realizing I didn't need that step and it is still long. I'll look at the other solutions later. It was a 5 kyu kata so it was hard. 
  I ran into an infinite loop today and debugged it. I'd like to learn more about debbugging. I think it's the one thing that by learning it everything else will be easier. That's the focusing question from The One Thing. 
  Outside of coding, I did more of the jQuery tutorial on lynda. It helped to take a break between my tutorial and coding. I think I might do more coding later because it's only 12:33 and I finished my tutroial, my coding, and my workout so I'm done really early.

### R1D8 - 1/8/19
I worked on a jQuery challenge from my lynda tutorial. I solved half of it. I didn't solve the other half because we hadn't actually learned the solution. So I was thrown off. I could have figured it out with some googling but I assumed I just missed the answer from the tutorial. But it wasn't actually in the tutorial. But the answer was similar to what I thought I just didn't know the properties/function I needed to know. Then I set up jasmine again, this time on a single html page. Then I set it up with my solution for the codewars kata, [Number of trailing zeros of N!](https://www.codewars.com/kata/52f787eb172a8b4ae1000a34/train/javascript). 

**Thoughts/Feelings:** I didn't take a break between my tutorials this morning and my coding hour. I think I work better when I take a break. It's nice to be done early now, but my break makes my actual work better and makes me more present. I was also distracted listening to shlomo talk to his friends on the phone. So I either have to ask Shlomo to be quiet or put clasical music or something on my head phones. Lesson: it's more important to be focused than to try to check off that I did something.

**Link To Work:** [Jasmine Single Page Addition](https://github.com/dangerousdashie/single_page_jasmine_simple)

### R1D7 - 1/7/19
I tried using Jasmine.js to run a simple unit test. [This](https://evanhahn.com/how-do-i-jasmine/) tutorial was helpful in getting started. Outside of my hour of coding I did an hour of a jQuery tutorial on lynda.com.

**Thoughts/Feelings:** Felt like I did more reading than coding but I'm glad I got one unit test written.

**Link To Work:** [Trying Out Jasmine](https://github.com/dangerousdashie/unit_testing/tree/master/jasmine-standalone-3.3.0)

### R1D6 - 1/6/19
Learned how to use Chrome DevTools to debug my javascript code. It seems more efficient than doing console.log() for everything. I tried to debug my code for the [Number of trailing zeros of N!](https://www.codewars.com/kata/52f787eb172a8b4ae1000a34/train/javascript) codewars kata. I'm having trouble because for some reason one variable is giving me a weird result when I += a number to it. It becomes undefined. 

I've also been doing lynda tutorials and other tutorials but I havn't been talking about them on here because we're not supposed to count tutorials for this challenge. But maybe I will start talking about what I'm learning on the tutorials just to track what I'm learning.

**Thoughts/Feelings:** Glad I finally started debugging in DevTools. I knew console.log() could not be the best way to debug. I would like to learn more about debugging.

### R1D5 - 1/5/19
Worked on the [Number of trailing zeros of N!](https://www.codewars.com/kata/52f787eb172a8b4ae1000a34/train/javascript) kata on code wars. I didn't finish it. I spent most of the time trying to understand factorials. Kind of annoying. I don't like when these katas make you learn something unrelated to programming. But I also couldn't stop once I started.

**Thoughts/Feelings:** Annoyed about having to learn about factorials. But I think I will finish it tomorrow.

### R1D4 - 1/4/19
Played with CSS grid. Wondering how to keep the grid items proportional even if the grid cells' proportions change. Is this possible? So in my example below; even though the grid cells can change to rectangular or square depending on the viewport, could I make the width and height for the grid item stay proporional. So in this case the grit items would always be cirlces and never become ovals even when the grid cell is a rectangle.

**Thoughts/Feelings:** Confused.

**Link To Work:** [grid_layout_practice](https://github.com/dangerousdashie/grid_layout_practice/tree/3dfe4f76488bf7f9eb0b7ea6bed602a9440e517b)

### R1D3 - 1/3/19
I made a program that uses the canvas api. When the user clicks a button it adds a random gradient to the canvas. I didn't like the blacks and grays. I realized I can use hsl color instead of rgb color to generate random colors with high saturation and medium to high lightness to avoid grays. I would love to try to make this so that the colors change depending on where the sun is in the sky. For example, bright colors in mid day, blues at twighlight, etc. Maybe there is an API for sun position?

**Link To Work:** [Random Color Generator](https://github.com/dangerousdashie/RandomColorGradient/tree/d450bcc4c9288c7fbf85a5c067178a3fb04a4b04)

### R1D2 - 1/2/19
Today I reviewed the other answered to the [Count the Digits](https://www.codewars.com/kata/566fc12495810954b1000030) kata. I tried it the way the top answer did it. Then I worked with the canvas API. Gonna try to make a Javascript program that draws using the canvas API.

### R1D1 - 1/1/19
I did some coding from scratch and memory; taking html input values as javascript function parameters. Then I finished the [Count the Digits](https://www.codewars.com/kata/566fc12495810954b1000030) kata I looked at yesterday. I started working on a different solution to the same kata before I submitted but I couldn't figure it out. It looks like some of the top answers are what I was trying to do. 

**Thoughts/Feelings:** Good day. Got some skeeter bites from coding outside. I feel a little overwhelmed when I see how good these other kata answers are. I'm going to review some of the top ones.

**Link To Work:** [Dashie's Solution to Count the Digits](https://www.codewars.com/kata/566fc12495810954b1000030/solutions/javascript/me/best_practice)

## Pre-Round-Log
### PR1D6 - 12/31/18
I coded in the passengers seat while driving to Fort Myers today. I re-coded [Random Bonus Points](https://github.com/dangerousdashie/Random_Bonus_Points/tree/eb7ebbbc6ace242072246ad62c8464a886ef47a3) from scratch and memory all over again. Then I added some responsive CSS. Then I went on code wars and started the [Count the Digits](https://www.codewars.com/kata/566fc12495810954b1000030) kata. I didn't get to coding the challenge but I made an HTML interface for the challenge. So really I worked on how to pass HTML input values as Javascript function parameters. I haven't put it on github yet. I might try to do some tutorials today too, but it's NYE so we'll see if I have time.

**Thoughts/Feelings:** Feeling good. Getting more comfortable with the stuff I'd been using but didn't really understand.

### PR1D5 - 12/30/18
I coded for around 1:40 hr. I spent today trying to rewrite [Random Bonus Points](https://github.com/dangerousdashie/Random_Bonus_Points) from scratch & memory to review what I learned. I ran into a bug  that only happened when my code was in a certain order. I spent so long trying to figure out why. I totally overcomplicated it. The solution was that the code was missing a semicolon!! I thought it had to do with something complex, something with how self executing functions work. OMG I spent so long on this stupid issue! I only figured it out because I posted it on stackoverflow and someone showed me. But I learned my lesson. I always thought semicolons were optional in javascript. Sometimes my code works without them. I guess they aren't! I might continue my jQuery tutorial or responsive design tutorial later today if I have time.

**Thoughts/Feelings:** Feels like I wasted so much time, but I know it's all part of the process. My back and eyes feel stressed. Need to take a break.

### PR1D4 - 12/29/18
I found out why the variable only works from inside the function. Since the script is run in the head of the HTML doc, the variable is created before the DOM. The DOM element doesn't exist yet, so it saves as null. I'm trying to figure out how to save it globally. I tried window.onload but it's not working. I also reviewed how to push to github from the command line and find files in unix. I pushed this project to github from the command line. 

**Update:** I did some more coding and I figured out the issue. I was calling the function that I set equal to onload, instead of just setting it equal to onload. Now my little program is working.  

**Link to work:** [Random Bonus Points](https://github.com/dangerousdashie/Random_Bonus_Points)

### PR1D3 - 12/28/18
I continued the project from yesterday. I was able to get a function called at random intervals. Now I'm dealing with an issue trying to reference a variable that stores a dom element. It only works from inside the function. But I thought outside the function, the variable is still in scope. What's going on? I coded for 65 minutes.


### PR1D2 - 12/27/18

I coded for an 1:19hr but not all at once. I practiced coding from scratch and from memory an html document with externally linked css and javascript. Then I tried to make a program that gave you points everytime you clicked a button. If it was during a randomized special bonus time you'd get extra points. This is a miniversion of a feature I want in a brain training app I'm making. I was having trouble getting the program to toggle the bonus time on and off at random times. Still working on this. It's too much of a mess to post on github. Should I post things that are a huge mess?

### PR1D1 - 12/26/18
I decided to do some preliminary days before the New Year starts. This way I can test power supply issues and other issues that come about from van lifing. Today, I coded for 63 minutes and 35 seconds. At the hour mark I was down to 68% power from 95%. I am looking into web dev environments I can use on my iPhone to save power.

Today I coded a very simple program. I used HTML, CSS, and Javascript. I wanted to stay away from jQuery, which I'm learning, because I want to make sure I have some experience with just Javascript so I'll be able to tell the two apart better in the future.

My program counts the number of times you click a button. My goal was to code this from scratch- no copy and pasting snipets of code. I started with a blank page and did the whole HTML/code by hand. I deleted it and started from a blank page a few times in a row until I could do it without any typos and errors.

Ok now I need to figure out how to push it to github and link it here. Power currently at 63%.

OK here it is! Not sure If I should have done it as a gist or repository. I still don't know how this all works.

**Link to work:** [Simple Button Click Counter](https://github.com/dangerousdashie/Simple-Button-Click-Counter)

Computer power now at 59%.

## Starting #100DaysOfCode
Dashie here, looking to start this challenge with the new year, **Jan 1, Tuesday, 2019.** Today is December 25th. I'm at a Starbucks in Tequesta, FL. I'm originally from Chicago, but I'm a nomad living in a Rialta, a van sized RV, with my spouse Shlomo. I decided to learn to code when our van broke down at a Barnes and Nobles in early 2017 and I bought a programming book. Now I'm looking to get more serious with my time so I'm commiting to this challenge. I already aim to spend 100 minutes a day doing coding tutorials, but I've decided to commit to an hour a day of hands on coding with this challenge. I'm not sure if my van's solar power set up will be able to handle the power required for these commitments, but I'm going to attempt the challenge anyways. I may still aim to just do 100 minutes a day, but use 60 minutes of that just for hands on coding and 40 minutes for tutorials. Ok, be back on Jan 1st!
