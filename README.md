# Índice

- [Introducción](#introducción)
  - [Instalación](#instalación)
    - [WSL](#wsl-windows-subsystem-for-linux)
    - [Node.js](#nodejs)
    - [TypeScript](#typescript)
    - [Next.js](#nextjs)
    - [MySQL](#mysql)
    - [Prisma](#prisma)
    - [Tauri](#tauri) (POR TERMINAR)
  - [Descripción](#descripción) (SIN REDACTAR)
  - [Uso](#uso) (SIN REDACTAR)

---

## Introducción

En esta guía redacto paso a paso como instalar cada una de las tecnologías mencionadas en el título de este repositorio así como la ejecución del proyecto creado. 

### Instalación

>[!NOTE]
>Lo suyo es instalar en el orden especificado las tecnologías, de lo contrario podría surgir algún que otro error inesperado. El orden será: WSL => Node.js => TypeScript => Next.js => MySQL => Prisma => Tauri

#### WSL (Windows Subsystem for Linux)

Primero instalaremos el entorno de trabajo Linux y ahí iremos instalando el resto de tecnologías:

1. Abrimos en Windows el programa ``Activar o desactivar las características de Windows``, una vez ahí activamos ``Plataforma de máquina virtual`` (*Virtual Machine Platform*) y ``Subsistema de Windows para Linux`` (*Windows Subsystem for Linux*).
2. Abrimos el `PowerShell` ejecutando como administrador y ejecutamos los siguientes comandos (hay un espacio antes de cada barra "/" en los comandos):
```shell
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart

dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
```
3. Una vez ejecutados los comandos anteriores, podemos ejecutar los siguientes:
```shell
wsl --update # Actualiza o instala WSL.
wsl --list --online # Muestra las distribuciones disponibles para instalar.
wsl --install -d Ubuntu-24.04 # Instala Ubuntu 24.04
wsl --set-default Ubuntu-24.04 # Establece Ubuntu 24.04 como la versión predeterminada al abrir WSL.
wsl --list --verbose # Muestra las distribuciones de Linux que tienes instaladas.
```

#### Node.js

1. Una vez en la consola de WSL, instalamos Node.js con algunas instalaciones previas. Primero nos aseguramos de que `curl` este instalado con `curl --version`, si no lo está, lo instalamos con `sudo apt install curl`. Una vez instalado `curl`, procedemos con la instalación de `NVM` y `Node.js` ejecutando los siguientes comandos:
```shell
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.0/install.sh | bash
# o, si el comando anterior no funciona, ejecutamos: nvm install --lts
```
2. Probablemente tengamos que reiniciar la terminal para escribir el comando para instalar Node.js:
```bash
nvm install 20
```
3. Verificamos las versiones y correcta instalación con:
```shell
node --version
npm --version
```

#### TypeScript

1. Una vez instalado `Node.js`, procedemos a instalar `TypeScript` con el siguiente comando:
```shell
npm install -g typescript
```
2. Verificamos su instalación con:
```shell
tsc --version
```

#### Next.js

1. Ahora crearemos un proyecto con ``Next.js`` con el comando `npx create-next-app@latest [nombre-proyecto]` (npm no deja nombrar proyectos con mayúsculas, asi que usaremos kebab-case):
```shell
npx create-next-app@latest example-app
```
2. Nos hará las siguientes preguntas sobre usar ciertas tecnologías y contestaremos:
	TypeScript? Yes 🟢
	ESLint? Yes 🟢
	Tailwind CSS? Yes 🟢
	"src/" directory? No 🔴
	App Router? Yes 🟢
	import alias ``(@/*)``? No 🔴
3. Finalmente se instalarán las dependencias de React, TypeScript, Tailwind, etcétera y ya tendremos el proyecto.

> [!NOTE]
> En el caso de que hubiésemos creado un proyecto con Node.js ejecutando el comando ``npm init -y`` y el comando ``npm install typescript ts-node @types/node --save-dev`` e inicianizando TS con ``npx tsc --init``, añadiremos Next.js al proyecto con los comandos:
> 
> ``npm install next react react-dom``
> ``npm install --save-dev @types/react @types/node``

#### MySQL

1. Instalamos MySQL ejecutando los siguientes comandos con los que también encenderemos el servidor y configuraremos el arranque automático:
```shell
sudo apt update && sudo apt upgrade -y
sudo apt install mysql-client
sudo apt install mysql-server
sudo systemctl start mysql
sudo systemctl status mysql #Comprobamos el estado.
sudo systemctl enable mysql #Para que se inicie automaticamente al encender el PC.
```
Entramos a la base de datos con:
```shell
sudo mysql -u root -p
```
2. Creamos un usuario y le damos todos los permisos así:
```sql
CREATE USER jesus IDENTIFIED BY '1243';
GRANT ALL PRIVILEGES ON *.* TO 'jesus'@'localhost'; [WITH GRANT OPTION]
```
Si el otorgar los permisos da error, usaremos "%" en vez de "localhost":
```sql
GRANT ALL PRIVILEGES ON *.* TO 'jesus'@'%';
```
Actualizamos los privilegios con:
```sql
FLUSH PRIVILEGES;
```
3. Nos logeamos con el usuario creado desde WSL con:
```shell
sudo mysql -u jesus -p
```
5. Creamos una base de datos y una tabla sencilla para probar que todo vaya bien:
```sql
CREATE DATABASE database1;
USE database1;
CREATE TABLE employee (
	id INT AUTO_INCREMENT PRIMARY KEY,
	first_name VARCHAR(50) NOT NULL,
	last_name VARCHAR(100) NOT NULL,
	dni VARCHAR(9) NOT NULL,
	salary INT
);
```
6. La URL de nuestra base de datos tiene la siguiente estructura:
```shell
[db_type]://[user]:[password]@[host]:[port]/[db_name]
```
Datos que podemos averiguar con estos comandos en MySQL:
- db_type: En nuestro caso sencillamente introducimos `mysql`
- user: `SELECT USER();`
- host: `SELECT @@hostname;`
- port: `SHOW VARIABLES LIKE 'port';`
- db_name: `SELECT DATABASE();`

7. Comandos útiles para la gestión de las bases de datos:
- `SHOW DATABASES;`
- `SHOW TABLES;`
- `DESC [nombre_tabla];
- `SELECT USER();`

#### Prisma

> [!NOTE]
> Lo suyo es tener instalado Visual Studio Code en nuestro WSL, lo instalaremos con `snap` ejecutando el comando:
> 
> `sudo snap install code --classic`
1. Ahora instalaremos Prisma en nuestro proyecto con el comando (en el directorio de nuestro proyecto, evidentemente):
```shell
npm install prisma --save-dev
```
2. Inicializamos Prisma con el comando:
```bash
npx prisma init
```
Esto creará los archivos `.env` (donde especificaremos la URL de nuestra base de datos) y `prisma/schema.prisma` donde especificaremos el provider de nuestra base de datos (mysql) y definiremos los `model`, que serían básicamente las tablas de nuestra base de datos.
3. Después de configurar los archivos mencionados (`.env` y `schema.prisma`) instalamos el Prisma Client con el comando:
```bash
npm install @prisma/client
```
Y generamos el cliente de nuestro ``schema.prisma`` definido con el comando:
```shell
npx prisma generate
```
> [!NOTE]
> Alternativamente, también podemos traer nuestra base de datos ya creada a nuestro `schema.prisma` con el comando:
> 
> `npx prisma db pull`
1. Finalmente migramos nuestra estructura y registros a nuestra base de datos MySQL con el comando (esto creará las tablas y la base de datos en nuestro MySQL): 
```shell
npx prisma migrate dev --name init
```
5. Si queremos insertar registros en nuestra base de datos lo haríamos con el código TypeScript de nuestro archivo `src/index.ts` como se especifica en el archivo de este repositorio index.ts(AÑADIR LINK) y ejecutando el comando:
```shell
npx ts-node src/index.ts
```

#### Tauri

1. Para instalar y usar Tauri primero debemos instalar una serie de paquetes para su uso así como Rust. Instalaremos Rust y Cargo con los comandos:
```shell
curl --proto '=https' --tlsv1.2 https://sh.rustup.rs -sSf | sh
sudo apt install cargo
```
También debemos instalar los siguientes paquetes para Linux:
```shell
sudo apt update
sudo apt install libwebkit2gtk-4.1-dev
sudo apt install build-essential
sudo apt install curl
sudo apt install wget
sudo apt install file
sudo apt install libxdo-dev
sudo apt install libssl-dev
sudo apt install libayatana-appindicator3-dev
```
2. Instalamos Tauri CLI con el comando (probablemente haya que reiniciar WSL antes de instalar Tauri):
```shell
npm install --save-dev @tauri-apps/cli@">1.0.0"
# En un entorno Rust puro ejecutaríamos "cargo install tauri-cli"
```
3. Después configuramos ciertos archivos para el correcto funcionamiento de Tauri. En `tsconfig.json`, en el apartado `exclude`, añadimos una línea con `"src-tauri"`. Después editaremos el archivo `next.config.mjs` para que quede de esta forma:
```js
/** @type {import('next').NextConfig} */
const nextConfig = {
  output: 'export',
}

export default nextConfig;
```
> [!NOTE]
> En la web de Tauri se asume que usamos un fichero `.js` y nos dice que escribamos `module.exports = nextConfig`, pero en nuestro caso se usa un fichero `.mjs`, así que deberemos escribir `export default nextConfig;`.
4. Para que `npm` detecte correctamente Tauri necesitas añadir a `scripts` en tu `package.json` lo siguiente:
```json
"scripts": {
  "tauri": "tauri"
}
```
5. Una vez configurados todos los archivos, ejecutaremos el siguiente comando para inicializar Tauri:
```shell
npm run tauri init
```
Se nos hará las siguientes preguntas a las que podemos responder lo siguiente:
- app name? proyecto-ejemplo
- window title? proyecto-ejemplo
- web assets located? ../out
- url of your dev server? http://localhost:3000
- frontend dev command? npm run dev
- frontend build command? npm run build
6. Es muy probable que Tauri no renderice en Windows, para solucionarlo usaremos el comando:
```shell
export WEBKIT_DISABLE_DMABUF_RENDERER=1
```
7. En principio, si todo ha ido bien, ya podríamos ejecutar nuestro proyecto base y nos saldría la ventana generada por Tauri, para ello usaremos el comando:
```shell
npm run tauri dev
```

==POR TERMINAR==
8. Puede que surjan algunos errores, como que no funcionan los botones en la ventana renderizada, para ello:
```shell
sudo usermod -aG video $USER

```

---

# README autogenerado por Next.js

This is a [Next.js](https://nextjs.org) project bootstrapped with [`create-next-app`](https://nextjs.org/docs/app/api-reference/cli/create-next-app).

## Getting Started

First, run the development server:

```bash
npm run dev
# or
yarn dev
# or
pnpm dev
# or
bun dev
```

Open [http://localhost:3000](http://localhost:3000) with your browser to see the result.

You can start editing the page by modifying `app/page.tsx`. The page auto-updates as you edit the file.

This project uses [`next/font`](https://nextjs.org/docs/app/building-your-application/optimizing/fonts) to automatically optimize and load [Geist](https://vercel.com/font), a new font family for Vercel.

## Learn More

To learn more about Next.js, take a look at the following resources:

- [Next.js Documentation](https://nextjs.org/docs) - learn about Next.js features and API.
- [Learn Next.js](https://nextjs.org/learn) - an interactive Next.js tutorial.

You can check out [the Next.js GitHub repository](https://github.com/vercel/next.js) - your feedback and contributions are welcome!

## Deploy on Vercel

The easiest way to deploy your Next.js app is to use the [Vercel Platform](https://vercel.com/new?utm_medium=default-template&filter=next.js&utm_source=create-next-app&utm_campaign=create-next-app-readme) from the creators of Next.js.

Check out our [Next.js deployment documentation](https://nextjs.org/docs/app/building-your-application/deploying) for more details.
