## useImperativeHandle React Hook Explained! 

 ## Setting some grounds
As you start exploring react.js, you discover it follows (with some exceptions) a unidirectional flow where data/actions in terms of props are transferred from the parent components to their children. 
A child, which maintains its state values can also trigger some actions in its parent via callbacks received as props. This way it can communicate with the parent node whenever it wants to in a declarative fashion.

Let's begin with an example where we are creating a badge icon creation component. The badge icon creation consists of two steps:
- **Step # 1:** A user can select an image for a badge
- **Step # 2:** When an image is selected in step # 1 then she can crop the image

We will discuss 2 scenarios with the given feature set. 


 
 ### Scenario # 1

In scenario #1 we have a Modal component with two children rendered conditionally so when a user selects an image file in step# 1 then step# 2 is rendered to crop the image.

![ezgif.com-gif-maker.gif](https://cdn.hashnode.com/res/hashnode/image/upload/v1655754181255/ZX7SyXz-G.gif align="left")

***Note: For simplicity, a lot of complex part from ImageCropper component is removed. We only care about the onCrop functionality there. ***

The code for this would be similar to:

```
// ImageCropper.js

import React, { useRef, forwardRef, useImperativeHandle } from 'react'

export const ImageCropper = ({ imgSrc, file, onCrop }) => {
  const imgRef = useRef<HTMLImageElement>(null)
  async function onImageCrop() {
     if (imgRef.current) {
        // DO SOME WORK TO CALCULATE CROP
       onCrop({ blobUrl: 'blob url from cropped image' })
    }
  }

  return (
    <div>
      {Boolean(imgSrc) && (
        <ImageCroppComponent >
          <img alt="Crop me" src={imgSrc} />
        </ImageCroppComponent>
      )}
    <button onClick={onImageCrop} > on Crop </button>
    </div>
  )
}

// Parent Component
<Dialog open={isVisible}>
  {step === 2 ? (
    <ImageCropper
      file={file}
      onCrop={({ blobUrl }: any) => {
        setImgSrc(blobUrl);
        setStep(1);
      }}
    />
  ) : (
    <FileInput imgSrc={imgSrc} onChange={handleFile} />
  )}
</Dialog>;
``` 

We will only focus on <ImageCropper /> component. Now, this is a simple unidirectional flow where ImageCropper is accepting a selected file from the parent and returns a cropped version whenever the ```
onCrop
``` function is called from inside of ImageCropper. 




### Scenario # 2
Now, this is where things get weird. Consider that we want to call ```
onCrop
``` directly from the parent. Let's say that we have added another field along with the image named **Title**. Now, we want to change UI and instead of the ImageCropper we are using the ```
Footer
``` of parent to crop image then save the badge.


![ezgif.com-gif-maker (1).gif](https://cdn.hashnode.com/res/hashnode/image/upload/v1655755768234/7Q-SxOZCG.gif align="left")

***One can argue that we can utilize the fellow state management library to achieve this but there are situations where the cleanest and most flexible option is the imperative handling direct from the parent. ***

## useImperativeHandle to save the day!
We have built a strong case to use the imperative handling, let's dive into the code.

***Note: Again for simplicity, a lot of complex part from ImageCropper component is removed. We only care about the onCrop functionality there. ***

```
// ImageCropper.js

import React, { useRef, forwardRef, useImperativeHandle } from 'react'

export const ImageCropper = forwardRef(({ imgSrc, file }, ref) => {
  const imgRef = useRef<HTMLImageElement>(null)
  useImperativeHandle(ref, () => ({
    async onCrop() {
      if (imgRef.current) {
        // DO SOME WORK TO CALCULATE CROP
        return { blobUrl: 'blob url from cropped image' }
      }
    },
  }))

  return (
    <div>
      {Boolean(imgSrc) && (
        <ImageCroppComponent >
          <img alt="Crop me" src={imgSrc} />
        </ImageCroppComponent>
      )}
    </div>
  )
})


// Parent Component

const cropperRef = React.useRef();
<Dialog open={isVisible}>
  {step === 2 ? (
    <ImageCropper ref={cropperRef} file={file} imgSrc={imgSrc} file={file} />
  ) : (
    <FileInput imgSrc={imgSrc} onChange={handleFile} />
  )}
  // Footer
  <div>
    {step === 2 ? (
      <Button onClick={() => setStep(1)}>Previous Step</Button>
    ) : null}
    <div>
      <Button onClick={onClose}>Cancel</Button>

      <BlueButton
        onClick={async () => {
          if (step === 2) {
            const { blobUrl } = (await cropperRef?.current?.onCrop()) ?? {};
            setImgSrc(blobUrl);
            setStep(1);
          } else {
            if (onSaveBadge) {
              onSaveBadge({
                icon_url: imgSrc,
                label: title,
                description: desc,
              });
            }
          }
        }}
        text="Save"
      />
    </div>
  </div>
</Dialog>;

```

### What's happening?
**useImperativeHandle** takes two arguments. The first is the ref that is exposed to the parent node.
Functional components are wrapped in forwardRef hook in order to bind the coming ref.
The second argument is a function that returns an object containing all of the properties or functions exposed to the parent component. Parent can access these properties by current property of the ref passed.

In this case, the parent can access the child's exposed onCrop function.

```
const { blobUrl } = (await cropperRef?.current?.onCrop()) ?? {};
```

## Conclusion
I have tried to showcase a use case for **useImperativeHandler**. It is a unique hook used in react which can be used to let the parent command child through imperative handling. It should be used only when other options are not viable. This is a kind of escape hatch & should not be used unless there are no other options.
If you liked the content or you have any feedback for improvement let me know in the comments!



