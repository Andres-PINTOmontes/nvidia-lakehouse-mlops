# 🚀 Guía de Despliegue a GitHub

## Opción 1: Usando Databricks Repos (Recomendado)

### Paso 1: Crear Repositorio en GitHub
1. Ve a https://github.com/new
2. Nombre: `nvidia-lakehouse-mlops`
3. Descripción: "End-to-end MLOps pipeline for NVIDIA stock prediction using Databricks"
4. Público o Privado (tu elección)
5. **NO inicialices** con README, .gitignore, o licencia
6. Clic en "Create repository"

### Paso 2: Configurar Databricks Repos
1. En Databricks UI, ve a **Repos** en la barra lateral
2. Clic en **Add Repo**
3. Git repository URL: `https://github.com/Andres-PINTOmontes/nvidia-lakehouse-mlops`
4. Git provider: GitHub
5. Repository name: `nvidia-lakehouse-mlops`
6. Clic en **Create Repo**

### Paso 3: Copiar Archivos al Repo
```bash
# En Databricks, ejecuta:
cp -r /Workspace/Users/4c9m0n7e5@gmail.com/databricks_nvda/* /Repos/<tu-usuario>/nvidia-lakehouse-mlops/
```

### Paso 4: Commit y Push desde Databricks
1. En Databricks Repos, ve a tu repositorio
2. Clic en el ícono de Git (rama)
3. Verás todos los archivos en "Changes"
4. Escribe mensaje de commit:
   ```
   Initial commit: NVIDIA Lakehouse MLOps Pipeline
   ```
5. Clic en **Commit & Push**
6. Autentícate con GitHub (Personal Access Token)

---

## Opción 2: Subida Manual (Más Simple)

### Paso 1: Descargar Proyecto
1. En este notebook, ejecuta el siguiente comando para crear un ZIP:

```python
import shutil
shutil.make_archive(
    '/tmp/nvidia-lakehouse-mlops', 
    'zip', 
    '/Workspace/Users/4c9m0n7e5@gmail.com/databricks_nvda'
)
print("✅ Archivo creado: /tmp/nvidia-lakehouse-mlops.zip")
```

2. Descarga el archivo desde `/tmp/nvidia-lakehouse-mlops.zip`

### Paso 2: Subir a GitHub
1. Ve a https://github.com/Andres-PINTOmontes/nvidia-lakehouse-mlops
2. Clic en **uploading an existing file**
3. Arrastra el archivo `.zip` o selecciónalo
4. GitHub detectará automáticamente que es un proyecto
5. Clic en **Commit changes**

---

## Opción 3: Git Local (Para Desarrollo Local)

### Requisitos
- Git instalado localmente
- Clonar tu repositorio de GitHub

### Pasos
```bash
# 1. Clonar repositorio vacío
git clone https://github.com/Andres-PINTOmontes/nvidia-lakehouse-mlops.git
cd nvidia-lakehouse-mlops

# 2. Copiar archivos del proyecto
# (Descarga el ZIP primero y extrae aquí)

# 3. Agregar archivos
git add .

# 4. Commit
git commit -m "Initial commit: NVIDIA Lakehouse MLOps Pipeline"

# 5. Push
git push origin main
```

---

## Verificación Post-Deployment

Después de subir, verifica que tienes:
- ✅ 6 notebooks (.ipynb) en las carpetas correctas
- ✅ README.md completo con documentación
- ✅ .gitignore para archivos de Databricks
- ✅ requirements.txt con dependencias
- ✅ Estructura de carpetas preservada

---

## Autenticación con GitHub

Para Databricks Repos o Git CLI, necesitarás un **Personal Access Token**:

1. Ve a GitHub Settings → Developer settings → Personal access tokens
2. Generate new token (classic)
3. Scopes necesarios: `repo` (acceso completo)
4. Copia el token (no podrás verlo de nuevo)
5. Úsalo como contraseña cuando Git te lo pida

---

## Troubleshooting

### Error: "dubious ownership"
```bash
git config --global --add safe.directory <path-to-repo>
```

### Error: "permission denied"
- Verifica que tienes permisos de escritura en el repo de GitHub
- Asegúrate de usar el Personal Access Token correcto

### Error: "failed to push"
- Verifica que el repositorio en GitHub está vacío
- Si no, usa `git pull origin main` primero

---

## Contacto

Si tienes problemas, abre un issue en:
https://github.com/Andres-PINTOmontes/nvidia-lakehouse-mlops/issues
