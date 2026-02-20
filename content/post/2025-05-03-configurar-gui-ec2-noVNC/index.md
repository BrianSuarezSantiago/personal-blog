---
title: Cómo configurar una GUI en una instancia EC2 usando noVNC
slug: "configurar-gui-ec2-novnc"
date: 2025-05-03T09:30:00+02:00
image: "https://d1hvrz5kjt6fwi.cloudfront.net/Blog Posts Covers/Posts Unorder/Post15.png"
draft: false
description:
tags:
  - AWS
  - EC2
categories:
  - AWS
  - Third-Party Tools on AWS
---

Si alguna vez has trabajado con una instancia EC2, sabes que AWS no proporciona una interfaz gráfica de usuario (GUI) por defecto. Aunque existen servicios gestionados como **Amazon WorkSpaces** o **NICE DCV**, estos pueden resultar menos flexibles o más costosos.

Pero no te preocupes, **podemos configurar nuestra propia GUI** en una instancia EC2. Para ello, utilizaremos **VNC**, un protocolo para compartir escritorios de manera remota, junto con **noVNC**, una interfaz web que nos permitirá acceder a la GUI desde cualquier navegador sin necesidad de instalar software adicional.

Vamos a ver cómo hacerlo paso a paso. 🚀

---

# Proceso de instalación y configuración

#### 1️⃣ Paso 1: Lanzar una instancia EC2

Primero, necesitamos una instancia EC2 en AWS. Si ya tienes una funcionando, puedes saltar este paso. Si no, sigue estos pasos:

- Ve a la consola de **AWS EC2** y haz clic en **"Launch Instance"**.

- Selecciona un AMI basado en **Debian**.

- Crea o selecciona un **par de claves SSH** para acceder a la instancia.

- Asegúrate de que el **grupo de seguridad** permite tráfico en el puerto **22 (SSH)**.

- Lanza la instancia y anota su **dirección IP pública**.

---

#### 2️⃣ Paso 2: Conectarse por SSH a la instancia

Una vez que la instancia esté en ejecución, usa SSH para conectarte desde tu terminal:

```bash
ssh -i "[NOMBRE_DE_TU_CLAVE].pem" admin@[DIRECCIÓN_IP_PÚBLICA]
```

Si todo está bien, deberías ver el terminal de tu instancia EC2.

---

#### 3️⃣ Paso 3: Instalar XFCE, TightVNC y noVNC

Ahora instalaremos el entorno de escritorio **XFCE**, junto con **TightVNC** (para la conexión VNC) y **noVNC** (para acceder desde el navegador).

Ejecuta los siguientes comandos en tu instancia EC2:

```bash
sudo apt-get update
sudo DEBIAN_FRONTEND=noninteractive apt-get install -q -y xfce4 xfce4-goodies tightvncserver dbus-x11 novnc
```

Esto instalará todos los paquetes necesarios.

---

#### 4️⃣ Paso 4: Crear un usuario para VNC

Para gestionar la sesión VNC, crearemos un usuario llamado `vncuser`:

```bash
sudo useradd vncuser -m
sudo passwd vncuser
```

Te pedirá que ingreses una contraseña. Usa una segura y anótala.

---

#### 5️⃣ Paso 5: Configurar TightVNC

Primero, crea la carpeta donde guardaremos la configuración de VNC:

```bash
sudo mkdir /home/vncuser/.vnc
sudo chown -R vncuser:vncuser /home/vncuser/.vnc
```

Ahora establece una contraseña para VNC:

```bash
sudo su -l -c 'vncpasswd' vncuser
```

Configura la resolución de pantalla (por ejemplo, **1920x1080**):

```bash
echo "geometry=1920x1080" | sudo tee /home/vncuser/.vnc/config
sudo chmod 600 /home/vncuser/.vnc/config
```

Configura XFCE como el entorno de escritorio predeterminado para VNC:

```bash
echo -e "#!/bin/bash\nxrdb $HOME/.Xresources\nstartxfce4 &" | sudo tee /home/vncuser/.vnc/xstartup
sudo chmod +x /home/vncuser/.vnc/xstartup
```

---

#### 6️⃣ Paso 6: Configurar noVNC para acceso desde el navegador

Para hacer que noVNC sea más accesible, lo configuraremos para que su interfaz web se abra directamente sin necesidad de añadir `/vnc.html` en la URL:

```bash
sudo ln -s /usr/share/novnc/vnc.html /usr/share/novnc/index.html
```

Ahora crearemos dos scripts para iniciar y detener noVNC fácilmente:

**Script para iniciar VNC (`startvnc.sh`)**

```bash
sudo nano /home/vncuser/startvnc.sh
```

Copia y pega el siguiente contenido:

```bash
#!/usr/bin/bash
/usr/bin/vncserver :$1
websockify -D --web=/usr/share/novnc/ $((6080-1+$1)) localhost:$((5901-1+$1))
```

Guarda el archivo y cierra el editor.

**Script para detener VNC (`stopvnc.sh`)**

```bash
sudo nano /home/vncuser/stopvnc.sh
```

Copia y pega lo siguiente:

```bash
#!/usr/bin/bash
kill $(lsof -ti tcp:$((6080-1+$1)))
/usr/bin/vncserver -kill :$1
exit 0
```

Guarda los cambios y cierra el archivo.

---

#### 7️⃣ Paso 7: Automatizar el inicio de noVNC con systemd

Para que noVNC se inicie automáticamente al reiniciar la instancia, configuraremos un servicio en **systemd**:

```bash
sudo nano /etc/systemd/system/vncserver@.service
```

Pega este contenido:

```ini
[Unit]
Description=Start VNC server at startup
After=syslog.target network.target

[Service]
Type=forking
User=vncuser
Group=vncuser
WorkingDirectory=/home/vncuser
PIDFile=/home/vncuser/.vnc/%H:%i.pid
ExecStartPre=/home/vncuser/stopvnc.sh %i
ExecStart=/home/vncuser/startvnc.sh %i
ExecStop=/home/vncuser/stopvnc.sh %i

[Install]
WantedBy=multi-user.target
```

Guarda y cierra el archivo. Luego, recarga systemd y habilita el servicio:

```bash
sudo systemctl daemon-reload
sudo systemctl enable vncserver@1.service
sudo systemctl start vncserver@1.service
```

---

#### 8️⃣ Paso 8: Permitir tráfico en el puerto de noVNC

Para acceder a la GUI desde el navegador, necesitamos abrir el puerto **6080** en el grupo de seguridad de la instancia:

1. Ve a la consola de **AWS EC2**.
2. Selecciona la instancia y abre la pestaña **"Security"**.
3. Haz clic en el grupo de seguridad y selecciona **"Edit inbound rules"**.
4. Agrega una nueva regla:
   - **Tipo:** Custom TCP
   - **Rango de puertos:** 6080
   - **Origen:** Anywhere (0.0.0.0/0)
5. Guarda los cambios.

---

#### 9️⃣ Paso 9: Acceder a la GUI desde el navegador

¡Listo! Ahora puedes abrir tu navegador y acceder a la interfaz gráfica escribiendo en la barra de direcciones:

```bash
http://[DIRECCIÓN_IP_PÚBLICA]:6080
```

Introduce la contraseña de VNC y verás el escritorio XFCE funcionando en tu instancia EC2.

# Despliegue por CloudFormation

Si quieres hacer el despliegue directamente a través de un stack de CloudFormation, puedes hacer uso del siguiente template:

```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: 'EC2 with browser supported GUI using noVNC'

Parameters:
  Ec2InstanceType:
    Type: String
    Default: t2.micro
    Description: Enter EC2 instance type to use for the instance

  Ec2Ami:
    Type: AWS::EC2::Image::Id
    Default: ami-0fec2c2e2017f4e7b
    Description: Enter AMI to use for the EC2 instance

  NoVncPort:
    Type: Number
    Default: 6080
    Description: Starting port for noVNC instance access

  VncDimensions:
    Type: String
    Default: 1280x720
    Description: Enter dimensions for VNC session

  VncPassword:
    Type: String
    Default: password
    Description: Enter password for noVNC access

Resources:
  VPC:
    Type: AWS::EC2::VPC
    Properties:
      CidrBlock: 10.0.0.0/16
      EnableDnsSupport: true
      EnableDnsHostnames: true
      Tags:
        - Key: Name
          Value: !Sub ${AWS::StackName}-vpc

  InternetGateway:
    Type: AWS::EC2::InternetGateway
    Properties:
      Tags:
        - Key: Name
          Value: !Sub ${AWS::StackName}-internet-gateway

  VPCGatewayAttachment:
    Type: AWS::EC2::VPCGatewayAttachment
    Properties:
      InternetGatewayId: !Ref InternetGateway
      VpcId: !Ref VPC

  PublicSubnet:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !Ref VPC
      CidrBlock: 10.0.1.0/24
      AvailabilityZone: !Select [ 0, !GetAZs ]
      MapPublicIpOnLaunch: true
      Tags:
        - Key: Name
          Value: !Sub ${AWS::StackName}-subnet


  PublicRouteTable:
    Type: AWS::EC2::RouteTable
    Properties:
      VpcId: !Ref VPC
      Tags:
        - Key: Name
          Value: !Sub ${AWS::StackName}-route-table

  PublicRoute:
    Type: AWS::EC2::Route
    DependsOn: VPCGatewayAttachment
    Properties:
      RouteTableId: !Ref PublicRouteTable
      DestinationCidrBlock: 0.0.0.0/0
      GatewayId: !Ref InternetGateway

  PublicSubnetRouteTableAssociation:
    Type: AWS::EC2::SubnetRouteTableAssociation
    Properties:
      SubnetId: !Ref PublicSubnet
      RouteTableId: !Ref PublicRouteTable

  SecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupName: !Sub ${AWS::StackName}-security-group
      GroupDescription: Security group for VNC access over noVNC
      VpcId: !Ref VPC
      SecurityGroupIngress:
        - Description: noVNC
          IpProtocol: tcp
          FromPort: !Ref NoVncPort
          ToPort: !Ref NoVncPort
          CidrIp: 0.0.0.0/0

  EC2Instance:
    Type: AWS::EC2::Instance
    Properties:
      ImageId: !Ref Ec2Ami
      InstanceType: !Ref Ec2InstanceType
      NetworkInterfaces:
        - AssociatePublicIpAddress: true
          DeviceIndex: "0"
          Description: Network interface for EC2 instance
          GroupSet:
            - !Ref SecurityGroup
          SubnetId: !Ref PublicSubnet
      Tags:
        - Key: Name
          Value: !Sub ${AWS::StackName}-server
      UserData:
          Fn::Base64: !Sub |
              #!/bin/bash
              sudo apt-get update
              sudo DEBIAN_FRONTEND=noninteractive apt-get install -q -y xfce4 xfce4-goodies tightvncserver dbus-x11 novnc

              sudo useradd vncuser -m
              sudo echo "vncuser:${VncPassword}" | sudo chpasswd
              sudo mkdir /home/vncuser/.vnc
              sudo chown -R vncuser:vncuser /home/vncuser/.vnc
              sudo su -l -c 'printf "${VncPassword}\n${VncPassword}\n\n" | vncpasswd' vncuser
              sudo chmod 600 /home/vncuser/.vnc/passwd
              sudo su -l -c 'echo -e "geometry=${VncDimensions}" > /home/vncuser/.vnc/config' vncuser
              sudo chmod 600 /home/vncuser/.vnc/config
              sudo su -l -c 'echo -e "#!/bin/bash\nxrdb $HOME/.Xresources\nstartxfce4 &" > /home/vncuser/.vnc/xstartup' vncuser
              sudo chmod +x /home/vncuser/.vnc/xstartup

              sudo ln -s /usr/share/novnc/vnc.html /usr/share/novnc/index.html

              sudo su -l vncuser -c 'echo "#!/usr/bin/bash
              /usr/bin/vncserver :\$1
              websockify -D --web=/usr/share/novnc/ \$((${NoVncPort}-1+\$1)) localhost:\$((5900+\$1))" > /home/vncuser/startvnc.sh'
              sudo chmod +x /home/vncuser/startvnc.sh

              sudo su -l vncuser -c 'echo "#!/usr/bin/bash
              kill \$(lsof -ti tcp:\$((${NoVncPort}-1+\$1)))
              /usr/bin/vncserver -kill :\$1
              exit 0" > /home/vncuser/stopvnc.sh'
              sudo chmod +x /home/vncuser/stopvnc.sh

              sudo sh -c 'echo "[Unit]
              Description=Start VNC server at startup
              After=syslog.target network.target
              [Service]
              Type=forking
              User=vncuser
              Group=vncuser
              WorkingDirectory=/home/vncuser
              PIDFile=/home/vncuser/.vnc/%H:%i.pid
              ExecStartPre=/home/vncuser/stopvnc.sh %i
              ExecStart=/home/vncuser/startvnc.sh %i
              ExecStop=/home/vncuser/stopvnc.sh %i
              [Install]
              WantedBy=multi-user.target" > /etc/systemd/system/vncserver@.service'

              sudo systemctl daemon-reload
              sudo systemctl enable vncserver@1.service
              sudo systemctl start vncserver@1.service

Outputs:
  AccessUrl:
    Description: URL to access the noVNC interface through a web browser
    Value: !Sub "http://${EC2Instance.PublicIp}:${NoVncPort}"
```
---

# Conclusión

Configurar una GUI en EC2 puede parecer complicado, pero con los pasos correctos, es completamente posible. Con noVNC, puedes acceder a tu escritorio desde cualquier navegador sin necesidad de instalar software adicional.

Si necesitas un entorno gráfico para trabajar con EC2, esta es una excelente solución sin depender de servicios costosos como Amazon WorkSpaces.

