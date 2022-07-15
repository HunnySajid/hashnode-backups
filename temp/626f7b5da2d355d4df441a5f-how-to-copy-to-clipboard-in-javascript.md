## How to copy to clipboard in JavaScript? 

 Copy-Paste is a fundamental need in application usage. Our users need this feature in the applications and websites as much as we developers need it while programming 😉. In this article, we will learn the JavaScript APIs available to help with copy-paste programmatically.

## What is Clipboard?
A `clipboard` is short-term storage to keep some information and data for some moment. The operating system provides this storage for applications to keep relevant data for a shorter duration such that the application/program can read it at a later point in time. The clipboard's content is usually managed in the computer's RAM(Random Access Memory).

## How to copy to the clipboard?
JavaScript provides [Asynchronous](https://www.youtube.com/watch?v=pIjfzjsoVw4) web APIs to copy the content to the clipboard. The JavaScript `navigator` object has the helpful `clipboard` API methods. The `writeText()' method is responsible for copying(or writing) to the clipboard.

```js
try {
    let value = input.value;
    if(navigator.clipboard) {   
      await navigator.clipboard.writeText(value);
      console.log(`The text '${value}' is in the Clipboard Now!`);
    } else {
      console.log(`Clipboard API is Not Supported`);
    }
  } catch (err) {
    console.error(`Failed to copy: ${err}`);
  }
```
The `writeText()` method takes the value to copy(or write to the clipboard) as an argument. You must also handle the error using a try-catch block. The code snippet above shows how to copy to the clipboard using the writeText method.

## How to paste?
The method `readText()` is used to read the content from the clipboard,, and thus, you can paste it to a target application.

```js
try {
    if (navigator.clipboard) {
      const fromClipboard = await navigator.clipboard.readText();
      input.value = fromClipboard;
      console.log(`The text '${fromClipboard}' is pasted successfully!`);
    } else {
      console.log(`Clipboard API is Not Supported`);
    }
  } catch (err) {
    console.error(`Failed to paste: ${err}`);
 }
```

## Support of the clipboard APIs in JavaScript
Several web browsers already support the `clipboard` APIs. You can check the current support [from here](https://caniuse.com/async-clipboard). As a measure, you must check for the `navigator.clipboard` availability using a simple `if-else` condition as we have seen in the code snippets above.

## Try it Out
You can try out the Copy-Paste functionality using JavaScript clipboard APIs from here,

%[https://webapis-playground.vercel.app/demo/clipboard]

Also, check the extensive API documentation from the [MDN Doc](https://developer.mozilla.org/en-US/docs/Web/API/Clipboard_API).

<hr />
That's all for now. I hope you found this article helpful.

Let's connect, I keep sharing tips & knowledge on these platforms:

- [Give a Follow on Twitter](https://twitter.com/tapasadhikary)
- [Communities on Showwcase](https://www.showwcase.com/atapas398)
- [Subscribe to my YouTube Channel](https://www.youtube.com/tapasadhikary?sub_confirmation=1)
- [Side projects on GitHub](https://github.com/atapas)

