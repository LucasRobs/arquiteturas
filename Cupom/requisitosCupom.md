## Requisitos funcionais:

#### Campanha
##### createCampaign(campaignData: CampaignData)
Cria uma campanha de cupons

- `interface CampaignData`: {
`code`: Código do cupon
`maxCoupons`: Número máximo de cupons a serem gerados
`restriction`: valor máximo de desconto.
`maxUse`: número máximo de usos por usuário.
`value`: valor do desconto
`firstPurchaseOnly`: apenas para primeira compra?
`active`: Status da campanha.
`idCampaignProfiles`: id do parceiro
}

Pseudo código:
```js
createCampaign(
  {
    code: string, 
    maxCoupons: number, 
    restriction: number, 
    maxUse: number, 
    value: number,
    firstPurchaseOnly: boolean, 
    active: boolean, 
    idCampaignProfiles: string[]
  }
  )
  se checkCouponCodeExists(code) return erro (código já existe)
  se não {
    incluir no banco: {
      …
      Campaingns:{
        [code]:{
        maxCoupons: number
        restriction: number
        maxUse: number
        firstPurchaseOnly: boolean
        active: boolean
        value: number
        idCampaignProfiles: string[] <- id dos perfis da campanha vinculada
        }
      },
      petFriends:{
        [idCampaignProfile]:{
          campaingns:[…, code]
        }
      }
    }
  }
```
  
##### checkCampainInProfile(code: string, idPetFriend: string)
Verifica se a campaign existe e se o perfil do usuário está vinculado a campanha	

`code`: Código do cupon
`idCampaignProfiles`: id do parceiro

```js
checkCampainInProfile(code, idPetFriend){
  se checkCampainCodeExists(code){
    campaign = getCampain(code)
    se campaign.idCampaignProfiles contem idPetFriend
      return campaign
  }
  return null
}
```

##### checkCouponCodeExists(code: string)
Verifica se a campanha existe
`code`: Código do cupon
```js
checkCampaingnCodeExists(code){
  se getCampain()
    return true
  return false
}
```

##### getCampain(code)
Retorna a campanha
`code`: Código do cupon
```js
getCampain(code){
  return get("campaingns/[code]")
}
```

#### Cupom
##### Gerar cupom
`generateCoupon(code)`: Gera cupons para uma campanha
- `code`: Código da campanha
```js
generateCoupon(code, idPetFriend){
  se checkCampainInProfile(code, idPetFriend){
      const canpaingn = getCampain(code)
        if(petFather.countReservations > 0){
          se canpaingn.firstPurchaseOnly return erro (primeira compra apenas)
        }
      return new coupom{
        idCoupon: uuid()
        code: code
        discount: canpaingn.value
        createdAt: Date.now()
      }
    }
  se não return erro (Cupon inválido)
}
```
##### Usar cupom
`useCoupon(reservationData: ReservationData)`: Usa um cupom
- `interface ReservationData`: {
 `cupom`: cupomProps
 `idPetFather`: id do cliente
 `idReservation`: id da reserva
 `idService`: id do serviço
}

```js
useCoupon(reservationData){
  se checkCampainInProfile(reservationData.cupom.code, reservationData.idPetFriend){
    const canpaingn = getCampain(reservationData.cupom.code)
    se (canpaingn.countUse < canpaingn.maxUse) {
      //campaingn
      adicionar 1 ao countUse
      adicionar idCoupon ao idActiveCoupons
      adicionar coupon.discount ao totalCampaingSaved

      
      //petFather
      adicionar +1 ao idPetFriend/couponsUse/idCoupon

      //coupon
      salva coupon em coupons/[idCoupon]
      
    }
  }
  return false
}
```
#####getCupom(idCoupon)
Retorna o cupom
`idCoupon`: id do cupom
```js
getCupom(idCoupon){
  return get("coupons/[idCoupon]")
}
```



Regras de negócio:
	
Descontos Cumulativos (não)

