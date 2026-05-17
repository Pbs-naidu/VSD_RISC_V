
## Calculator with counter
```bash
\m5_TLV_version 1d: tl-x.org
\m5
   
   // ============================================
   // Welcome, new visitors! Try the "Learn" menu.
   // ============================================
   
   //use(m5-1.0)   /// uncomment to use M5 macro library.
\SV
   // Macro providing required top-level module definition, random
   // stimulus support, and Verilator config.
   m5_makerchip_module   // (Expanded in Nav-TLV pane.)
\TLV
   // Connect SV inputs to TLV pipesignals.
    
    
      $cnt[4:0] =reset ? (1+ >>1$cnt) : 1;
    
   $reset = *reset; 
    $val1[31:0] = (>>1$out[31:0]);
   $val2[31:0] = $rand2[3:0];
   $sum[31:0] = $val1[31:0] + $val2[31:0];
   $diff[31:0] = $val1[31:0] - $val2[31:0];
   $prod[31:0] = $val1[31:0] * $val2[31:0];
   $quot[31:0] = $val1[31:0] / $val2[31:0];
   $out[31:0] = ($op[1:0] == 2'b00)
                        ? $sum[31:0] : 
                       ($op[1:0] == 2'b01)   
                        ? $diff[31:0] :
                       ($op[1:0] == 2'b10)  
                        ? $prod[31:0] :
                         $quot ;
            $out [31:0] = reset ? 0 : $out[31:0];
       
      
         
   //...
   
   // Assert these to end simulation (before the cycle limit).
   *passed = *cyc_cnt > 40;
   *failed = 1'b0;
\SV
   endmodule
```
<img width="1918" height="782" alt="calc with counter" src="https://github.com/user-attachments/assets/ef7a5224-e6bc-429c-9c42-443df2705d88" />

## Calculator with two stage calc with counter and valid mux
```bash
    |calc
      @1
         $reset = *reset;
      ?$valid
         @1
            $val1[31:0] = (>>2$out[31:0]);
            $val2[31:0] = $rand2[3:0];
            $sum[31:0] = $val1[31:0] + $val2[31:0];
            $diff[31:0] = $val1[31:0] - $val2[31:0];
            $prod[31:0] = $val1[31:0] * $val2[31:0];
            $quot[31:0] = $val1[31:0] / $val2[31:0];
            $valid[4:0] = $reset ? (1+ >>1$valid) : 1;
         @2
            $resetmux = ($reset || !$valid);
            $out[31:0] = $resetmux ? 0:
                            ($op[1:0] == 2'b00)? $sum[31:0] : 
                             ($op[1:0] == 2'b01)   ? $diff[31:0] :
                             ($op[1:0] == 2'b10)  ? $prod[31:0] :
                              $quot ;
```
<img width="1918" height="877" alt="calc_counter_valid_mux" src="https://github.com/user-attachments/assets/7288d391-9e71-4ddd-ac37-21c3d6974087" />

## Calculator with Modified resetmux Condition at 2nd stage
```bash
 |calc
      @1
         $reset = *reset;
      ?$valid
         @1
            $val1[31:0] = (>>2$out[31:0]);
            $val2[31:0] = $rand2[3:0];
            $sum[31:0] = $val1[31:0] + $val2[31:0];
            $diff[31:0] = $val1[31:0] - $val2[31:0];
            $prod[31:0] = $val1[31:0] * $val2[31:0];
            $quot[31:0] = $val1[31:0] / $val2[31:0];
            $valid[4:0] = $reset ? (1+ >>1$valid) : 1;
            
         @2
            $out[31:0] = $resetmux ? $reset || $valid:
                            ($op[1:0] == 2'b00)? $sum[31:0] : 
                             ($op[1:0] == 2'b01)   ? $diff[31:0] :
                             ($op[1:0] == 2'b10)  ? $prod[31:0] :
                              $quot ;
````
<img width="1918" height="827" alt="modification on reset mux" src="https://github.com/user-attachments/assets/f0eb50ed-69a2-44b4-8c57-0dc2cbd53660" />

## calculator with memory added
```bash
|calc
      @1
         $reset = *reset;
      ?$valid
         @1
            $val1[31:0] = (>>2$out[31:0]);
            $val2[31:0] = $rand2[3:0];
            $sum[31:0] = $val1[31:0] + $val2[31:0];
            $diff[31:0] = $val1[31:0] - $val2[31:0];
            $prod[31:0] = $val1[31:0] * $val2[31:0];
            $quot[31:0] = $val1[31:0] / $val2[31:0];
            $valid[4:0] = $reset ? (1+ >>1$valid) : 1;
            
         @2
            $out[31:0] = $resetmux ? $reset || $valid:
                            ($op[2:0] == 3'b000)? $sum[31:0] : 
                             ($op[2:0] == 3'b001)   ? $diff[31:0] :
                             ($op[2:0] == 3'b010)  ? $prod[31:0] :
                             ($op[2:0] == 3'b011)  ? $quot[31:0] :
                             ($op[2:0] == 3'b100)  ? >>2$mem[31:0] :
                             $mem[31:0];
                             
            $mem[31:0] = $reset ? 0 :
                         ($op[2:0] == 3'b101)  ? >>2$mem[31:0] :
                          >>2$out[31:0] ;
```
<img width="1918" height="876" alt="calculator with memory1" src="https://github.com/user-attachments/assets/7b2c5261-174b-4e9f-9aca-8aa8a39806f7" />
<img width="1918" height="822" alt="calculator with memory2" src="https://github.com/user-attachments/assets/2bf31629-3e69-46a2-ab0f-3cabfa47dde8" />



