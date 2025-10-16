# 🧰 Troubleshooting - Windows 7 no macOS (Apple Silicon) com UTM

Este arquivo reúne **os problemas mais comuns** ao executar Windows 7 no macOS com chip Apple Silicon utilizando o UTM, além de suas respectivas **causas e soluções práticas**.

---

## 🪟 1. Tela Azul (BSOD) — `VEoxMouse.sys`

**Sintoma:** Tela azul logo após logar no Windows 7.  
**Causa provável:** Driver de mouse VMware legado, incompatível com UTM.  

**Solução:**
1. Inicie a VM em **Modo de Segurança** (`F8` no boot).  
2. Acesse `C:\Windows\System32\drivers\`.  
3. Renomeie ou delete `VEoxMouse.sys`.  
4. Reinicie a máquina.

---

## 🖥️ 2. Tela preta ou travada ao iniciar

**Sintoma:** A VM liga, mas a tela fica preta.  
**Causas prováveis:** Driver de vídeo incompatível ou configuração incorreta de boot.

**Soluções:**
- Configure o display como **VGA** (sem aceleração 3D).  
- Desative **UEFI Boot** no UTM e use BIOS Legacy.  
- Verifique integridade da imagem QCOW2:
```bash
qemu-img check W7_Windows7.qcow2
```

---

## 🌐 3. Sem conexão de rede dentro do Windows

**Sintoma:** O Windows não recebe IP ou não acessa a rede.  
**Causas prováveis:** Driver de rede ausente ou interface errada.

**Soluções:**
- Confirmar que a placa de rede está como `e1000` no UTM.  
- Instalar driver Intel Pro/1000 no Windows 7.  
- Verificar IP com `ipconfig`.

---

## 🛜 4. Não consigo acessar a VM de fora do host

**Sintoma:** Host (macOS) acessa a VM, mas outras máquinas não.  
**Causa:** Rede NAT do UTM não expõe serviços externamente.

**Soluções:**
- Configurar **Port Forwarding** no UTM.  
- Ou usar **Bridged Mode**, se disponível.

---

## 💿 5. Erro ao converter a imagem `.vmdk`

**Sintoma:** Falha no comando `qemu-img convert`.  
**Causas:** Arquivo corrompido, caminho incorreto ou permissões.

**Soluções:**
- Validar hash MD5:
```bash
md5 W7_Appliance.ova
```
- Extrair novamente com `tar -xvf`.  
- Converter usando caminhos absolutos:
```bash
qemu-img convert -O qcow2 "/path/W7_Appliance-disk001.vmdk" "W7_Windows7.qcow2"
```

---

## 🔐 6. BSOD `0x0000007B` (INACCESSIBLE_BOOT_DEVICE)

**Sintoma:** Tela azul logo no boot.  
**Causa:** Driver de controladora SATA/VirtIO incompatível.

**Solução:** Configurar a interface de disco no UTM como **IDE**.

---

## 🧠 7. Performance muito lenta

**Sintoma:** Windows extremamente lento ou travando.  
**Causas:** Emulação x86 em ARM (naturalmente mais lenta) e recursos insuficientes.

**Soluções:**
- 2 vCPUs e 4 GB de RAM no mínimo.  
- Desativar recursos desnecessários no Windows.  
- Usar versões mais leves do Windows 7.

---

## 📁 8. Erro ao iniciar a VM no UTM

**Sintoma:** UTM fecha ou mostra erro.  
**Soluções:**
- Verificar permissões do arquivo `.qcow2`.  
- Confirmar arquitetura `x86_64`.  
- Verificar integridade do disco.

---

## 🧭 9. Problemas de rede entre VMs (UTM + VirtualBox)

**Sintoma:** Ubuntu → Windows funciona, Windows → Ubuntu não.  
**Causa:** NAT unidirecional do VirtualBox.

**Soluções:**
- Configurar **Port Forwarding** no VirtualBox.  
- Criar rota + NAT no macOS host.  
- Usar Bridged Mode.

---

## 🛑 10. Problemas com teclado/mouse

**Soluções:**
- Habilitar “PS2 Controller” na configuração do UTM.  
- Desativar drivers externos (VMware Tools, VirtualBox Guest Additions).  
- Usar dispositivos padrão.

---

✍️ **Autor:** Seu Nome Aqui  
📅 **Última atualização:** Outubro de 2025
