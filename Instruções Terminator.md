Instruções Terminator

Instalação do terminal (Terminator) para Windows.

Paso a passo da instalação

1 - Abra o PowerShell como administrador e cole isso:
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux

2 - Instale o Ubuntu pelo seguinte link:
https://www.microsoft.com/store/produ...

3 - Depois da instalação e configuração do user/senha, faça o seguinte no terminal:
 - sudo apt install dbus-x11
 - sudo systemd-machine-id-setup

4 - Instale o X-server pelo seguinte link:
https://sourceforge.net/projects/vcxsrv/

5 - Depois de instalar o X-server, deve clicar sobre o atalho
criado na área de trabalho, propriedades e acrescentar isso em:
"C:\Program Files\VcXsrv\vcxsrv.exe" :0 -ac -terminate -lesspointer -multiwindow -clipboard -wgl -dpi auto

6 - Inicie X-server.

7 - Instale o Terminator no Bach do ubuntu.
 - sudo apt-get update
 - sudo apt-get install terminator

8 - Após a instalação, execute esse comando:
 - DISPLAY=:0 terminator &

A saída deve ser o terminator em execução.
