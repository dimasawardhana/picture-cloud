# Minimize HTTP Request for Faster Performance

Most of the webpages nowadays, has more than 70 HTTP request on the first loads and it really affects the user experience especially the load times.

have you ever tried gone into a website, and wait for a long time before the first page appear? yes, that's because there are a lot of data being requested at that time, and browser cannot display any single data to the browser because it needs to complete fetching all the data before it can display a webpage you requested.

So, to overcome this **slow performance**, let's talk about how minimizing http request can help.

## 🧠 Why should we minimize the request ?

By Reducing the number of requests you made:

1. **Faster page load**  
   The fewer assets to load, the less time users wait.

2. **Time to First Paint (TTFP) and Time to Interactive (TTI)**  
   Some of the data you're requesting, may become blocking resources to a browser to initialize the display on the webpage. if the data become less, the browser will display the webpage faster.

3. **Core Web Vitals**  
   Improving metrics like Largest Contentful Paint (LCP) and First Input Delay (FID) because of fewer requests block on rendering path.

4. **Mobile Experience**  
   Making sure that the webpage still accessible even on the slow network, because only few request needed to make the webpage displaying.

## :gear: Then, what can we do to minimize the request?

Minimizing by just remove files could make some of the webpage feature unusable or even malfunctioned. Here are the things we can do to minimize the request while keep the feature usable.

1. :package: **Bundling CSS and JS Files**  
   Bundlers like **Webpack**, **Vite** or **Parcel** combining multiple files into fewer files, and that's why its called bundler. This mechanism reduces request overhead. Mostly, using modern frontend framework , the bundle is done per **component** or **route** by chunks which is called **code splitting**.  
   &nbsp;  
   ![Component to Chunks](https://github.com/dimasawardhana/picture-cloud/blob/main/component-to-chunks.webp?raw=true "Component to Chunks")

2. :pencil: **Inline Critical CSS**  
   It's common to divide between CSS file and HTML file, whereas it can be combined into one like it's placed in the head tag inside style tag. When requesting for a page, all the file required will be fetched and this takes time. Inlining Critical CSS means adding the critical CSS (the content which is displayed on the first load) inline with HTML file. This will avoid blocking path when rendering, resulting a faster load.

3. :national_park: **Use WebP/AVIF for Images**  
   WebP or AVIF format, typically 25-35% smaller than JPEG or PNG images, but still maintaining the picture quality. By having smaller file size to fetch, the request speed will be faster since it's small.  
   &nbsp;  
   Tips: Use `<img loading='lazy' />` if the image is not required to be displayed instantly. this will reduce loading time too.

4. :scissors: **Remove Unused Scripts, Fonts, and Styles**  
   This technique is also used to minimized file fetched, which resulting faster load too. to remove unused styles we can use library like **PurgeCSS/UnoCSS**. We can use **tree-shaking, only import the used component**.  
   &nbsp;  
   So, instead of using this:

   ```javascript
   import * as Icons from "react-icons/fi";
   ```

   use this :

   ```javascript
   import { FiEdit2 } from "react-icons/fi";
   ```

   &nbsp;

5. :stopwatch: **Lazy load Component**  
   Lazy loading component fetches the component only when it is needed. This technique creates separate chunks to load. So, when the component is not used, it won't be loaded. making the initial load of the page faster.  
   To implement Lazy loading in React/NextJS, we can use `React.Lazy()` along with `<Suspense />`.  
   &nbsp;  
   Here is the example:

   ```javascript
   const Chart = React.lazy(() => import("./components/Chart"));
   // "Lazy" import to import it when it's needed

   // wrap use Suspense to replace the main component with placeholder before the main component fully loaded
   return (
     <Suspense fallback={<Loading />}>
       <Chart />
     </Suspense>
   );
   ```

   Even these techniques would likely boost performance speed on your page load, be mind that you **may not need** these in some cases. **Optimize the code when you need it**.

## 📚 References

1. [web.dev – Reduce JavaScript Payloads](https://web.dev/reduce-javascript-payloads/)
2. [vite-plugin-svg-icons](https://github.com/vbenjs/vite-plugin-svg-icons)
3. [UnoCSS](https://github.com/unocss/unocss)
4. [MDN – WebP and AVIF Images](https://developer.mozilla.org/en-US/docs/Web/Media/Formats/Image_types)
5. [Next.js Docs – Static Optimization](https://nextjs.org/docs/advanced-features/automatic-static-optimization)
6. [Vercel Blog – CSS Support](https://vercel.com/blog/next-css)
7. [Vite Build Docs](https://vitejs.dev/guide/build.html)
8. [vite-plugin-critical](https://github.com/anncwb/vite-plugin-critical)
9. [Addy Osmani's Critical CSS tool](https://github.com/addyosmani/critical)
10. [webpack Docs – Code Splitting](https://webpack.js.org/guides/code-splitting/)
11. [Vite Docs – Chunking Strategy](https://vitejs.dev/guide/build.html#chunking-strategy)
12. [React Docs – Code Splitting](https://reactjs.org/docs/code-splitting.html)
