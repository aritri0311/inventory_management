# Keys

1. **DH**: Design House
2. **CM**: Chiplet Manufacturers
3. **ICM**: IC Manufacturers
4. **CD**: Chiplet Distributors
5. **ICD**: IC Distributors
6. **SI**: System Integrators
7. **PT**: Part Type name
8. **PID**: Part ID

# Enums
```
Enum TransferStatus(Enum):
  IN_TRANSFER = 0
  NOT_IN_TRANSFER = 1

Enum DeploymentStatus(Enum):
  DEPLOYED = 0
  NOT_DEPLOYED = 1
```
# Register a Part Type
```
  function RegisterPartType(PT,DH):
    if CurrentParticipant not DH/CM/ICM then
      throw ERROR
    partTypeExist = Fetch Part Type with Key PT
    if partTypeExist != Null then
      throw ERROR
    else
      newPartType = partType(PT,DH)
      newPartType.part = {}
      newPartType.designHouse = DH
    Update newPartType in Ledger
```

  
# Register Parts
```
  function RegisterParts(PT,PIDs):
    if CurrentParticipant not DH/CM/ICM then
      throw ERROR
    partTypeExist = Fetch Part Type with Key PT
    if partTypeExist == Null then
      throw ERROR
    For all PID in PIDs do
      partExist = Fetch Part with Key PID from partTypeExist
      if partExist != Null then
        throw ERROR
      newPart = part(ID,CurrentParticipant) 
      newPart.manufacturerName = CurrentParticipant/manufacturerName
      newPart.currentOwner = CurrentParticipant
      newPart.ownerRole = CurrentParticipantRole
      newPart.transferStatus = TransferStatus.NOT_IN_TRANSFER
      newPart.deploymentStatus = DeploymentStatus.NOT_DEPLOYED
      newPart.trace.append(CurrentParticipant)
      Update PartType with Key PT with newPart with Key PID in Ledger
```

# Transfer Parts
```
  function TransferParts(PT,PIDs,newOwner):
     partTypeExist = Fetch Part Type with Key PT
     if partTypeExist == Null then
        throw ERROR
     For all PID in PIDs do
        partDetails = Fetch Part Details with Key PID from partTypeExist
        if partDetails == Null then
          throw ERROR
        if partDetails.currentOwner != CurrentParticipant then
          throw ERROR
        partDetails.transferStatus = TransferStatus.IN_TRANSFER
        partDetails.transferTo = newOwner
        Update PartType with Key PT with newPart with Key PID in Ledger
```

# Confirm Received Parts
```
  function ConfirmPartReceive(PT,PIDs):
    partTypeExist = Fetch Part Type with Key PT
    if partTypeExist == Null then
      throw ERROR
    For all PID in PIDs do
      partDetails = Fetch Part Details with Key PID from partTypeExist
        if partDetails == Null then
          throw ERROR
        if partDetails.transferTo != CurrentParticipant then
          throw ERROR
        partDetails.currentOwner = CurrentParticipant
        partDetails.transferTo = ""
        partDetails.transferStatus = transferStatus.NOT_IN_TRANSFER
        partDetails.trace.append(CurrentParticipant)
        Update PartType with Key PT with newPart with Key PID in Ledger
```     
    
