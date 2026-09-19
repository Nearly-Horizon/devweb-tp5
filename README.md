=================================
Question 1.1 :
=================================
HTTP/1.1 200 OK
Date: Fri, 18 Sep 2026 23:30:29 GMT
Connection: keep-alive
Keep-Alive: timeout=5
Transfer-Encoding: chunked

=================================
Question 1.2 :
=================================
HTTP/1.1 200 OK
Content-Type: application/json
Date: Fri, 18 Sep 2026 23:32:08 GMT
Connection: keep-alive
Keep-Alive: timeout=5
Content-Length: 20

=================================
Question 1.3 :
=================================
sec-ch-ua
"Google Chrome";v="153", "Not_A Brand";v="8", "Chromium";v="153"
sec-ch-ua-mobile
?0
sec-ch-ua-platform
"Windows"
upgrade-insecure-requests
1
user-agent
Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/153.0.0.0 Safari/537.36

=================================
Question 1.4 :
=================================
# Erreur affichée dans la console
Error: ENOENT: no such file or directory, open 'C:\Users\goujo\OneDrive\Bureau\INFO\L2\S4\Dev Web\TP\devweb-tp5\index.html'
    at async open (node:internal/fs/promises:1360:25)
    at async Object.readFile (node:internal/fs/promises:2149:14) {
  errno: -4058,
  code: 'ENOENT',
  syscall: 'open',
  path: 'C:\\Users\\goujo\\OneDrive\\Bureau\\INFO\\L2\\S4\\Dev Web\\TP\\devweb-tp5\\index.html'
    }

ENOENT (No such file or directory): Commonly raised by fs operations to indicate that a component of the specified pathname does not exist. No entity (file or directory) could be found by the given path.

# Modification du .catch() pour renvoyer une 500
function requestListener(_request, response) {
  fs.readFile("index.html", "utf8")
    .then((contents) => {
      response.setHeader("Content-Type", "text/html");
      response.writeHead(200);
      return response.end(contents);
    })
    .catch((error) => {
      console.error(error);
      response.writeHead(500);
      return response.end("<html><p>500: INTERNAL SERVER ERROR</p></html>");
    });
}

# Après renommage de __index.html en index.html
Le serveur renvoie 200 et le contenu du fichier.

=================================
Question 1.5 :
=================================
# requestListener en async/await
async function requestListener(_request, response) {
  try {
    const contents = await fs.readFile("index.html", "utf8");
    response.setHeader("Content-Type", "text/html");
    response.writeHead(200);
    return response.end(contents);
  } catch (error) {
    console.error(error);
    response.writeHead(500);
    return response.end("<html><p>500: INTERNAL SERVER ERROR</p></html>");
  }
}

=================================
Question 1.6 :
=================================
### npm install cross-env --save : a ajouté "cross-env" dans "dependencies" et créé le dossier node_modules/ + package-lock.json

### npm install nodemon --save-dev : a ajouté "nodemon" dans "devDependencies"

=================================
Question 1.7 :
=================================
# http-dev
Variable = NODE_ENV=development
Outil = nodemon (rechargement automatique de la page web)
Usage = Développement web

nodemon surveille les fichiers et redémarre automatiquement --> pratique en dev

# http-prod
Variable = NODE_ENV=production
Outil = node ( pas de rechargement automatique de la page web)
Usage = Production web

node lance juste le script une fois --> plus performant en prod

=================================
Question 1.8 :
=================================
# Code HTTP
URL : http://localhost:8000/index.html
Code HTTP : 200
URL : http://localhost:8000/random.html
Code HTTP : 200
URL : http://localhost:8000/
Code HTTP : 404
URL : http://localhost:8000/dont-exist
Code HTTP : 404

# requestListener async/await modifié
async function requestListener(request, response) {
  response.setHeader("Content-Type", "text/html");
  try {
    const contents = await fs.readFile("index.html", "utf8");
    const urlParts = request.url.split("/"); // ["", "random", "5"]

    switch (urlParts[1]) {
      case "":
      case "index.html":
        response.writeHead(200);
        return response.end(contents);

      case "random.html":
        response.writeHead(200);
        return response.end(
          `<html><p>${Math.floor(100 * Math.random())}</p></html>`,
        );

      case "random": {
        const nb = Number.parseInt(urlParts[2], 10);
        if (Number.isNaN(nb)) {
          response.writeHead(400);
          return response.end("<html><p>400: BAD REQUEST</p></html>");
        }
        const numbers = Array.from({ length: nb })
          .map(() => Math.floor(100 * Math.random()))
          .join("</li><li>");
        response.writeHead(200);
        return response.end(`<html><ul><li>${numbers}</li></ul></html>`);
      }

      default:
        response.writeHead(404);
        return response.end("<html><p>404: NOT FOUND</p></html>");
    }
  } catch (error) {
    console.error(error);
    response.writeHead(500);
    return response.end("<html><p>500: INTERNAL SERVER ERROR</p></html>");
  }
}

Après ajout de la route /random/:nb avec split("/") et fall-through
sur "" et "index.html", les routes / et /index.html renvoient tous
deux 200 avec le contenu de index.html, et /random/n renvoie n nombres
aléatoires (400 si n n'est pas un entier).