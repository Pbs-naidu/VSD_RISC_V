##  Program Counter
<img width="1458" height="510" alt="image" src="https://github.com/user-attachments/assets/544d70a3-ce25-4d9e-ae08-2747d19b9e26" />

### Each instruction in this CPU is 32 bits (4 bytes) long.
### So one instruction occupies 4 memory locations because each location stores 1 byte.
```bash
    |pc
      @0
         
         $reset = *reset;
         $pc[31:0] = >>1$reset ? 32'd0 :
                                      >>1$inc_pc[31:0];
      @1
         $inc_pc[31:0] = $pc[31:0] + 32'd4;

```
## Result:
<img width="1918" height="823" alt="image" src="https://github.com/user-attachments/assets/78957cd6-117b-46b5-9e73-85f04bd5788c" />


