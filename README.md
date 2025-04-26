# Resume Portfolio

![Vue.js](https://img.shields.io/badge/Vue.js-3.0-green?style=flat-square&logo=vue.js) 
![Accessible](https://img.shields.io/badge/Accessible-AA-blue?style=flat-square&logo=accessibility) 
![Responsive Design](https://img.shields.io/badge/Responsive-Design-orange?style=flat-square&logo=responsive)

A dynamic and responsive Vue.js application designed to showcase a professional resume and portfolio. This project highlights technical expertise, leadership experience, and proficiency in modern web development frameworks. It features a clean, accessible design with sections for a summary, core competencies, professional experience, education, and awards. The application dynamically renders content from a Markdown file, ensuring easy updates and scalability.

## ✨ Features

- **Dynamic Resume Rendering**: Content is dynamically loaded from a Markdown file (`README.md`) for easy updates.
- **Responsive Design**: Fully responsive layout optimized for desktop and mobile devices.
- **Accessibility**: Built with W3C accessibility standards in mind, ensuring an inclusive user experience.
- **Modern Frameworks**: Built with Vue.js 3, leveraging its reactive and component-based architecture.
- **Navigation**: Smooth navigation with hash-based routing for quick access to different sections.
- **Customizable**: Easily extendable to include additional sections or features.
- **Secure Data Handling**: Ensures no personal or PCI data is leaked by using a separate server for sensitive content.

## ⚙️ Project Setup

### Install Dependencies
```bash
npm install
```

### Run the Development Server
```bash
npm run dev
```

### Build for Production
```bash
npm run build
```

### Preview the Production Build
```bash
npm run serve
```

### Lint and Fix Files
```bash
npm run lint
```

## 📂 Folder Structure

```
resume-portfolio/
├── public/                 # Static assets
│   ├── favicon.ico         # Favicon
│   ├── index.html          # HTML template
│   └── README.md           # Markdown file for dynamic content
├── src/                    # Source code
│   ├── assets/             # Images and other assets
│   ├── components/         # Vue components
│   │   ├── Footer.vue      # Footer component
│   │   ├── Loader.vue      # Loader component
│   │   ├── NavigationMenu.vue # Navigation menu component
│   │   └── ResumeHeader.vue # Resume header component
│   ├── router/             # Vue Router configuration
│   ├── App.vue             # Root Vue component
│   └── main.js             # Application entry point
├── .gitignore              # Git ignore rules
├── jsconfig.json           # JavaScript configuration
├── package.json            # Project metadata and dependencies
├── vite.config.js          # Vite configuration
└── README.md               # Project documentation
```

## 🛠️ Technologies Used

- **Vue.js 3**: Frontend framework for building user interfaces.
- **Vue Router**: For navigation and routing between sections.
- **Marked**: For parsing Markdown into HTML.
- **HTML5 & CSS3**: For semantic structure and styling.
- **JavaScript (ES6+)**: For interactivity and logic.

## ♿ Accessibility Features

- Skip links for keyboard navigation.
- High contrast color scheme for readability.
- Semantic HTML structure for screen readers.
- Responsive design for mobile and desktop users.

## 🔍 How It Works

1. The application fetches the `README.md` file from a server or falls back to a local file in the `assets/` folder.
2. The Markdown content is parsed into HTML using the `marked` library.
3. The parsed content is dynamically rendered in the `ResumeView` component.
4. Navigation links allow users to jump to specific sections of the resume.

## ✏️ Customization

To customize the content:

1. Edit the `src/assets/README.md` file to update the resume and portfolio content.
2. Modify the `ResumeView.vue` component to adjust the layout or add new sections.
3. Update styles in `App.vue` or `ResumeView.vue` to match your branding.

## 🚀 Deployment

To deploy the application:

1. Build the project for production:
   ```bash
   npm run build
   ```
2. Deploy the contents of the `dist/` folder to your preferred hosting platform (e.g., Netlify, Vercel, GitHub Pages).

---

## 🛡️ Setting Up a Secure Server for Sensitive Data

To avoid leaking personal or PCI data, you can set up a secure server to serve sensitive content like your resume. Below is a step-by-step guide to spin up a server like `#folder:readme-server`:

### Step 1: Create a Node.js Server
1. Install Node.js if you don’t already have it.
2. Create a new folder for your server (e.g., `readme-server`).
3. Inside the folder, create a file named `server.js` with the following content:

   ```javascript
   const http = require('http');
   const fs = require('fs');
   const path = require('path');

   const PORT = process.env.PORT || 3001;
   const readmePath = path.join(__dirname, 'README.md');

   const server = http.createServer((req, res) => {
     res.setHeader('Access-Control-Allow-Origin', '*');
     if (req.url === '/readme' && req.method === 'GET') {
       fs.readFile(readmePath, 'utf8', (err, data) => {
         if (err) {
           res.writeHead(500, { 'Content-Type': 'text/plain' });
           res.end('Internal Server Error');
           return;
         }
         res.writeHead(200, { 'Content-Type': 'text/markdown' });
         res.end(data);
       });
     } else {
       res.writeHead(404, { 'Content-Type': 'text/plain' });
       res.end('Not Found');
     }
   });

   server.listen(PORT, () => {
     console.log(`Server running at http://localhost:${PORT}/`);
   });
   ```

### Step 2: Add a `README.md` File
Place your sensitive resume content in a `README.md` file in the same folder as `server.js`.

### Step 3: Run the Server
Run the server using the following command:
```bash
node server.js
```

### Step 4: Update the Frontend
In your Vue.js app, update the `fetchMarkdown` function to point to your server:
```javascript
const serverUrl = import.meta.env.VITE_README_SERVER_URL || 'http://localhost:3001/readme';
```

### Step 5: Deploy the Server
Deploy the server to a secure hosting platform like Vercel or AWS, ensuring proper access controls are in place.

---

## 📜 License

This project is licensed under the MIT License. See the `LICENSE` file for details.

---

Built with ❤️ using Vue.js.