# Meminimalkan HTTP Request untuk Performa yang Lebih Cepat

Sebagian besar halaman web saat ini, memiliki lebih dari 70 HTTP Request pada saat pertama kali dimuat dan hal ini sangat mempengaruhi pengalaman pengguna terutama _loading time_.

Pernahkah Kamu mencoba masuk ke sebuah situs web, lalu menunggu waktu yang lama sebelum halaman pertama muncul? Nah, hal ini disebabkan karena ada banyak data yang diminta pada saat itu, dan browser tidak dapat menampilkan satu pun data ke browser karena browser harus menyelesaikan pengambilan semua data yang diperlukan sebelum dapat menampilkan halaman web yang diminta.

Jadi, untuk mengatasi **performa yang lambat** ini, mari kita bahas bagaimana meminimalkan HTTP request dapat membantu.

## 🧠 Mengapa kita harus meminimalkan request?

Dengan mengurangi jumlah request :

1. **Pemuatan halaman lebih cepat**  
   Semakin sedikit _asset_ yang dimuat, semakin sedikit waktu yang dihabiskan pengguna untuk menunggu.

2. **Time to First Paint (TTFP) dan Time to Interactive (TTI)**  
   Beberapa data yang Anda minta, dapat menjadi _blocking resources_ ke browser untuk menginisialisasi tampilan pada halaman web. Jika data menjadi lebih sedikit, browser akan menampilkan halaman web lebih cepat.

3. **Core Web Vitals**
   Meningkatkan metrik seperti Largest Contentful Paint (LCP) dan First Input Delay (FID) karena request yang diblokir pada _rendering path_ lebih sedikit.

4. **Mobile Experience**  
   Memastikan bahwa halaman web tetap dapat diakses meskipun pada jaringan yang lambat, karena hanya sedikit request yang diperlukan untuk menampilkan halaman web.

## :gear: Lalu, apa yang bisa dilakukan untuk meminimalisir Request?

Meminimalisir dengan cara menghapus file dapat mengakibatkan beberapa fitur tidak bisa digunakan atau bahkan malfungsi. Berikut adalah beberapa hal yang bisa dilakukan untuk meminimalisir _request_ namun tetap mempertahankan fitur yang ada tetap berfungsi.

1. :package: **_Bundling File_ CSS dan JS**  
   Bundlers seperti **Webpack**, **Vite** atau **Parcel** menggabungkan beberapa _file_ menjadi _file_ yang lebih sedikit. Mekanisme ini mengurangi _request overhead_. Kebanyakan di framework modern, _bundle_ dibuat per **component** atau **route** sebagai _chunks_ yang biasa disebut **code splitting**.  
   &nbsp;  
   ![Component to Chunks](https://github.com/dimasawardhana/picture-cloud/blob/main/component-to-chunks.webp?raw=true "Component to Chunks")

2. :pencil: **Inline Critical CSS**  
   Membagi antara file CSS dan HTML menjadi file terpisah adalah hal yang biasa, yang padahal dapat digabungkan menjadi satu file yang sama dengan cara menuliskan css dalam tag `<Head />`. Ketika membuat _request_ untuk sebuah _page_, semua _file_ yang dibutuhkan akan diambil dari server dan hal tersebut membutuhkan waktu.  
    **_Inlining Critical CSS_** berarti menambahkan konten CSS yang kritis (yang perlu segera dimunculkan karena ditampilkan saat _first load_) pada file HTML. This will avoid blocking path when rendering, resulting a faster load Hal ini akan menghindari _blocking path_ saat melakukan pe-_render_-an, yang menghasilkan _page_ lebih cepat dimuat.

3. :national*park: **Menggunakan format WebP/AVIF untuk Gambar**  
   WebP atau AVIF format, memiliki ukuran 25-35% lebih kecil daripada gambar berformat JPEG atau PNG, namun tetap menjaga kualitas dari gambar tersebut. Dengan memiliki ukuran file yang lebih kecil untuk diambil, maka kecepatan \_request* akan lebih cepat.  
   &nbsp;  
   Tips: Use `<img loading='lazy' />` if the image is not required to be displayed instantly. this will reduce loading time too.

4. :scissors: **Menghilangkan Script, Fonts, dan Styles yang tidak digunakan**  
   Tehnik ini juga digunakan untuk meminimalisir file yang akan diambil dari server, yang akan menghasilkan kecepatan muatan yang lebih cepat juga. to remove unused styles we can use library like **PurgeCSS/UnoCSS** Untuk menghilangkan **_styles_** yang tidak digunakan pada suatu _page_. Kita dapat menggunakan **_tree-shaking_** yaitu hanya meng-_import_ file yang digunakan.  
   &nbsp;  
   Sehingga, daripada menggunakan ini:

   ```javascript
   import * as Icons from "react-icons/fi";
   ```

   gunakan ini :

   ```javascript
   import { FiEdit2 } from "react-icons/fi";
   ```

   &nbsp;

5. :stopwatch: **Lazy load Component**  
   **_Lazy loading component_** mengambil **component** hanya saat itu benar - benar diperlukan. Tehnik ini membuat _chunks_ terpisah untuk dimuat. Sehingga, ketika _component_ tidak digunakan, maka _component_ tersebut tidak akan dimuat. Hal ini membuat waktu _initial load_ dari suatu _page_ lebih cepat.  
   &nbsp;  
   Untuk mengimplementasikan _Lazy loading component_ pada React/NextJS, kita dapat menggunakan `React.Lazy()` bersamaan dengan `<Suspense />`.  
   &nbsp;  
   Here is the example:

   ```javascript
   const Chart = React.lazy(() => import("./components/Chart"));
   // import component yang diperlukan

   // Wrap dengan Suspense untuk mengganti component dengan component sementara sebelum component utama dimuat
   return (
     <Suspense fallback={<Loading />}>
       <Chart />
     </Suspense>
   );
   ```

   Meskipun tehnik - tehnik tersebut dapat menaikan kecepatan performa pada _page load_, perlu diingat bahwa, bisa jadi kita **tidak perlu menggunakan tehnik tersebut** pada beberapa kasus. **Optimasi kodenya hanya ketika kita memerlukannya**.

## 📚 Referensi

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
