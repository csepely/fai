# FAI - Fully Automatic Installation

FAI nevű eszköz tesztelésére jött létre.

## Branches

### main

Vagrant környezetben létrehoz két VM-et. **fai** az _install server_, **demohost** az _install client_.

### efi

Az előzőhöz képest annyival több, hogy EFI boot lehetősége van a **demohost** gépnek, ellenőrizd az EFI image elérési útvonalát.

### pub_net

A korábban létrehozott **fai** gépet publikus interfészre ültetjük, fizikailag kikerül a hálózatra, érdemes elszeparálni a már működő hálózattól, mert DHCP
kéréseket tud kiszolgálni. Hozzáadtam egy osztályt, a cikkben jelzett helyre, paraméterekkel hozzáadható a kívánt géphez.

[További részletek...](https://csepely.hu/red-pill/work/fai/)
