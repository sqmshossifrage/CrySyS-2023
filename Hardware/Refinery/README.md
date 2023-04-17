As the challenge suggests, it uses [`Modbus`](https://en.wikipedia.org/wiki/Modbus) and [ladder logic](https://en.wikipedia.org/wiki/Ladder_logic). I then found an excellent specification of [Modbus Functions Codes](https://ozeki.hu/p_5873-modbus-function-codes.html) online. 

Commands to Modbus uses a general format of: hex representation of address + function code + function specification. After playing with the operator machine for a while, we figure out that for example, command `420100690001` means to interact with the "column tank controller" `0x42`, perform "read coils" `0x01`, with address `0x0069` (the 2-byte address is calculated by computing the decimal expression minus one $106-1 = 105$ and converting to hex `105 = 0x0069`), and read only one coil `0x0001`. The relay `42010100` means: column tank controller `0x42`, with function `0x01`, has a reply of `0x01` byte, where the data itself is `0x00`. This means the coil has reading `0`. If the coil has reading 1, it replies `42010180`. 

For registers, use function code `0x04` to read, for example `690400c10001` to read the "e5 sensor". 

Knowing how to control the refinery, we interact directly with the "flag controller" and it returns something looks promising! Decode and [enjoy your flag](https://www.youtube.com/watch?v=dQw4w9WgXcQ) 
```
b'\xa2\x04\xbeNever gonna give you up Never gonna let you down Never gonna run around and desert you Never gonna make you cry Never gonna say goodbye Never gonna tell a lie and hurt you'
```

We manually control the refinery and realize, if we open both output valves of the ethanol tank, the tank will deplete in 3 minutes IRL. It took me some time to figure out why does this mean: look at the output - they are [E5 and E10](https://en.wikipedia.org/wiki/Common_ethanol_fuel_mixtures) tanks! I need to control the percentage of ethanol in both tanks by controlling the valves. My strategy is then
1. Initialization with manual mode and other stuff to keep the petroleum supply including open gasoline valves for both E5 and E10. 
2. Open ethanol valve for E5, wait for 60 seconds IRL, close the valve. 
3. Open ethanol valve for E10, wait for 120 seconds IRL, close the valve. 
4. Wait for 900 seconds IRL, then close gasoline valve for E10. 
5. Wait for 60 seconds IRL, then close gasoline valve for E5. 
6. Read the flag and actually enjoy your flag this time. 
