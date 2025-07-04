# **Manual: Implementación de Chef Workstation para Equipos**  

## **🔧 Paso 1: Instalaciones Iniciales (Todos)**  
1. **Descargar e instalar:**  
   - [Chef Workstation](https://downloads.chef.io/products/workstation)  
   - [VirtualBox](https://www.virtualbox.org/)  
   - [Vagrant](https://www.vagrantup.com/)  

2. **Verificar instalaciones (ejecutar en CMD/PowerShell):**  
   ```bash
   chef --version
   vagrant --version
   vboxmanage --version
   ```

---

## **🖥️ Paso 2: Configurar Entorno Local**  
### **2.1 Crear máquina virtual (cada miembro)**  
1. Ejecutar:  
   ```bash
   mkdir chef-vm && cd chef-vm
   vagrant init ubuntu/focal64
   vagrant up
   ```

2. **Configurar IP única (evitar conflictos):**  
   Editar el `Vagrantfile` y cambiar:  
   ```ruby
   config.vm.network "private_network", ip: "192.168.56.10"  # Miembro 1: .10, Miembro 2: .11, etc.
   ```

---

## **👨‍💻 Paso 3: Desarrollo del Cookbook**  
### **3.1 Estructura básica (Líder crea repositorio Git)**  
```
web_server/
├── recipes/
│   ├── default.rb
│   ├── install_apache.rb
│   └── firewall.rb
└── templates/
    └── index.html.erb
```

### **3.2 Ejemplo de receta (`install_apache.rb`)**  
```ruby
package 'apache2' do
  action :install
end

service 'apache2' do
  action [:start, :enable]
end
```
### **3.3 Ejemplo de receta (`install_nginx.rb`)** 
```ruby
package 'nginx' do  
  action :install
end

service 'nginx' do 
  action [:start, :enable]
end

# Opcional: Eliminar archivo default de Nginx
file '/etc/nginx/sites-enabled/default' do
  action :delete
end
---

## **🚀 Paso 4: Despliegue en Equipo**  
### **4.1 Todos clonan el repositorio:**  
```bash
git clone https://github.com/tu-equipo/web_server.git
```

### **4.2 Aplicar cookbook (cada miembro):**  
```bash
knife bootstrap 192.168.56.10 --ssh-user vagrant --sudo --node-name nodo_miembro1 --run-list 'recipe[web_server]'
```

---

## **✅ Paso 5: Verificación**  
| **Check**            | **Comando**               | **Resultado esperado**       |
|-----------------------|---------------------------|------------------------------|
| Apache instalado      | `systemctl status apache2`| "active (running)"           |
| Firewall configurado  | `sudo ufw status`         | "80/tcp ALLOW"               |

---


---

## **📎 Recursos Adicionales**  
- [Documentación Chef](https://docs.chef.io/)  
