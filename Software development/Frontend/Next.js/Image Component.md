Image are extremely hard to manage. It may look easy. but it is hard. They are often heaviest to load. which will hamper web optimization, SEO and overall website performance.

Next.js automatically optimizes the image for different devices size and resolution. This ensures fast loading times and a better user experience. And images are only loaded when they come into the viewport thus reducing the initial page load time. next.js optimizes images by serving them in modern format (like webP) and generating multiple sizes for different screen resolution.

It prevents layout shift. It hamper SEO and is big burden in the eye.
### Props in next.js
###### width and height
For local image, we don't need width and height as we already know that. next.js calculate the intrinsic width and height bases on imported file. Thus it determines image ratio. However we need to provide for remote images as next.js doesn't have access to remote files during the build process.
###### Layout
It determines how the image behaves in different context(e.g. `fill`, `fixed` or `resonsive`)

### Why should I use next.js Image tag
Questions like don't react do image optimization ? and other things that next.js image does ? comes a lot 

Well only server side language can optimize the image before it is served to the client. Only sever side language can resize the image before it is served to the client.

It also does automatic compression also. Like changing to WebP format.

It also enhance the SEO, It is always rendered in such a way to avoid cumulative layout shift, a core web vital that google is going to use in search ranking.

Upon request, next.js changes the image format to WebP format when it is supported. It also reduces the image size also. Rather than serving in JPEF and PNG, it tries to serve in WebP format. Webp does better compression rather than those JPEG and PNG.

And it also have `placeholder-blur` features, (meaning that small blurred version is loaded first to improve perceived load time).

It is similar to `<img />` tag with slight differences.
### Best practices
1. Set width and height attributes to ensure that the browser can allocate space for image to prevent layout shift. 
2. Sending big image to mobile is just not good. use `Cloudinary’s Responsive Breakpoint Generator` to generate different image for different devices
3. Set priority for important images
4. Optimize images before upload. Always keep image below 1Mb.
5. Consider using WebP(have superior lossy/lossless compression without quality loss, faster browser load and broader support also) where possible. JPEG is great for photos, PNG is better for images with transparency. There are tools like `WebP Converter` to help
6. Compress images like TinyPng or ImageOptim can be used to compress the image before they are uploaded to your side
7. Using a CDN like cloudinary or Imgix can help deliver optimized images to users more quickly by caching images closer to the user's location