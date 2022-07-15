## ZigZag traverse that binary tree ⚡ 

 Today we are going to solve two DS problems that are actually very similar. 
They are respectively :-
* [level order traversal in binary tree](https://leetcode.com/problems/binary-tree-level-order-traversal/)
* [zigzag order traversal in binary tree](https://leetcode.com/problems/binary-tree-zigzag-level-order-traversal/)

And I like to solve them in a specific way which comes natural to me. 

---

### Binary Tree Level Order Traversal

![Hashnode_level_order_traversal.gif](https://cdn.hashnode.com/res/hashnode/image/upload/v1632665250474/Y3ysQegoi.gif)

Here is the code which works :-

```js
var levelOrder = function(root) {
    if(!root)
        return [];
    let currentQueue = [];
    let nextNodes = [];
    const output = [[root.val]];
    currentQueue.push(root);
    while(currentQueue.length){
        const node = currentQueue.shift();
        nextNodes = nextNodes.concat(findNextNodes(node));
        if(!currentQueue.length && nextNodes.length){
            currentQueue = nextNodes;
            nextNodes = [];
            output.push(currentQueue.map(node=>node.val));
        }
    }
    return output;
};

const findNextNodes=(node)=>{
    const nextNodes = [];
    if(node.left){
        nextNodes.push(node.left);
    }
    if(node.right){
        nextNodes.push(node.right);
    }
    return nextNodes;
}
```

**Explanation :-**
* First we check whether `root` of binary tree exists or not. If not, simply return empty array `[]`.
* Initialize a `currentQueue` and `nextNodes` array. 
* Initialize a `output` 2D array where for each level we maintain an array consisting of the level nodes. Initially, the `[root.val]` will be the first valid entry. So `output` will be `[[3]]`.
* And then push the current `root` node inside `currentQueue`. So `currentQueue` will be `[Node(3)]`.
* Now, we will loop over the `currentQueue` until it is empty. 
* Being a queue, we **dequeue** the first entry in it using `shift` operator on `currentQueue`.
* Now, we pass this entry or `node`(`Node(3)`) inside `findNextNodes` function whose purpose it to return us a list of children of the passed `node`. 
* `findNextNodes` initializes an empty `nextNodes` array and pushes the `left` and `right` children of `node` if they exist and returns the `nextNodes` array. `nextNodes` will be `[Node(9),Node(20)]`.
* Then the returned array is concatenated with `nextNodes`. `nextNodes` will be `[Node(9),Node(20)]`.
* Now before the end of the loop, if `currentQueue` is empty and `nextNodes` is not, `currentQueue` starts referring the `nextNodes` array and `nextNodes` is reinitialized to `[]`. Also, all the `node.val` values in the `currentQueue` are pushed in the form of an array to `output`. By this step, output will be `[[3],[9,20]]`. 
* This way the for each level we will successfully obtain the **left to right** level order traversal in the `output` array. 

---

### Binary Tree Zigzag Level Order Traversal

![Hashnode_zigzag_level_order_traversal.gif](https://cdn.hashnode.com/res/hashnode/image/upload/v1632665262182/7MFrPhUfe.gif)

Before we see the code and explanation, it's important to note that we will be converting the above solution to obtain the current one. I would like to stress on this derivation because this is how I particularly enjoyed the process of arriving at this solution. We will do it in two iterations so that the process of arriving at this solution seems more intuitive. 

Alright, here is the first iteration :-

```js
var zigzagLevelOrder = function(root) {
    if(!root)
        return [];
    let currentQueue = [];
    let nextNodes = [];
    let isNextLevelEven = true;
    const output = [[root.val]];
    currentQueue.push(root);
    while(currentQueue.length){
        const node = currentQueue.shift();
        nextNodes = nextNodes.concat(findNextNodes(node,isNextLevelEven));
        if(!currentQueue.length && nextNodes.length){
            currentQueue = nextNodes;
            nextNodes = [];
            isNextLevelEven = !isNextLevelEven;
            output.push(currentQueue.map(node=>node.val));
        }
    }
    return output;
};

const findNextNodes=(node,isNextLevelEven)=>{
    const nextNodes = [];
    if(node.left){
        nextNodes.push(node.left);
    }
    if(node.right){
        nextNodes.push(node.right);
    }
    return isNextLevelEven?nextNodes.reverse():nextNodes;
}
```

**Explanation :-**
* As soon as you see the above code, you will realize that all it does is add subtle nuances to **level order traversal's** code. 
* The first nuance is the `isNextLevelEven` variable. So let's discuss it. When we think about a **zigzag** traversal, it starts on **level 1** i.e. `root` node from **left to right** and then on **level 2** from **right to left** and the again from **left to right** on **level 3**. So there is a **alternating** state that's being introduced. Alternating state can be depicted using a **boolean** and that's what we are doing here. If `root` is **level 1** i.e. an **odd** level, then the **next level** would be **even**. This is why I have initialized `isNextLevelEven` to `true` in the starting of this code. 
* Before the start of the loop, `output` will be `[[3]]` and `currentQueue` will be `[Node(3)]`.
* Now, we will loop over the `currentQueue` until it is empty. 
* Being a queue, we **dequeue** the first entry in it using `shift` operator on `currentQueue`.
* Now, we pass `node`(`Node(3)`) and `isNextLevelEven` inside `findNextNodes` function whose purpose it to return us a list of children of the passed `node` according to `isNextLevelEven` boolean variable. 
* `findNextNodes` like before initializes an empty `nextNodes` array and pushes the `left` and `right` children of `node` if they exist. The change comes in the **returning** value step where now exists a **ternary** condition where `isNextLevelEven` if true will return the **reversed** `nextNodes` (for right to left) array else the `nextNodes` (for left to right) as it is . In this step, since `isNextLevelEven` is `true`, the returned value will be `[Node(20),Node(9)]`.
* Then the returned array is concatenated with `nextNodes`. `nextNodes` will be `[Node(20),Node(9)]`.
* Now before the end of the loop, if `currentQueue` is empty and `nextNodes` is not, `currentQueue` starts referring the `nextNodes` array and `nextNodes` is reinitialized to `[]`.  We also flip the `isNextLevelEven` boolean. Also, all the `node.val` values in the `currentQueue` are pushed in the form of an array to `output`. By this step, output will be `[[3],[20,9]]`. 
* The algorithm seems reasonable till here. But let's see what happens in next iteration of while loop.
* Now `currentQueue` being `[Node(20),Node(9)]`, `node` will be `Node(20)` due to `shift` operation.
* Now since next level is 3 which is **odd**, `[Node(15),Node(7)]` will be concatenated with `nextNodes`. 
* And again in next iteration of while loop, `node` will be `Node(9)` due to `shift` operation resulting in `nextNodes` being `[Node(15),Node(7),Node(23),Node(43)]`. 
* Now when the `if` condition inside the `while` loop holds true, `output` will become `[[3],[20,9],[15,7,23,43]]` which clearly is wrong because the last level is not traversed from left to right as we wanted to. 

The step where we went wrong here is assuming that `currentQueue` well has to be a **queue**. Being a **queue**, we will never be able to start a traversal from the **last** element. For instance, after we got correct `output` till **level 2**, we would have wanted, that for **level 3**, first the **children** of `Node(9)` were pushed from **left to right** and then for `Node(20)` but instead the reverse happened. This is literally how I formed the second intuition that it's my **last** element from which the **next level** of nodes should be traversed. And the Data Structure which operates primarily on the **last** element is none other than a **stack**. 

So all we need is to replace the `shift` operation with `pop` and things will work as we intend to. Obviously, it's better to rename `currentQueue` to `currentStack` for brevity. 

So here is the working code :-
```js
var zigzagLevelOrder = function(root) {
    if(!root)
        return [];
    let currentStack = [];
    let nextNodes = [];
    let isNextLevelEven = true;
    const output = [[root.val]];
    currentStack.push(root);
    while(currentStack.length){
        const node = currentStack.pop();
        nextNodes = nextNodes.concat(findNextNodes(node,isNextLevelEven));
        if(!currentStack.length && nextNodes.length){
            currentStack = nextNodes;
            nextNodes = [];
            isNextLevelEven = !isNextLevelEven;
            output.push(currentStack.map(node=>node.val));
        }
    }
    return output;
};

const findNextNodes=(node,isNextLevelEven)=>{
    const nextNodes = [];
    if(node.left){
        nextNodes.push(node.left);
    }
    if(node.right){
        nextNodes.push(node.right);
    }
    return isNextLevelEven?nextNodes.reverse():nextNodes;
}
```
---

The second question was asked to me in a startup's interview in early stage of my career. That time I didn't know JS and was interviewing for a Java Dev position. I didn't do well in that interview and kind of dreaded this problem. Later one day while solving the **level order traversal** on leetcode, I realised that the **zigzag** problem is an extension of this and then implemented it in same manner as I documented in this article. That **connection** between both problems was cool to arrive at. 

### Thank you for your time :D

