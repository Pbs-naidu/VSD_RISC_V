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

## Fetch
## Instruction Memory
<img width="1424" height="493" alt="image" src="https://github.com/user-attachments/assets/aaeb3b03-1354-424f-94fc-3686cef2f69d" />

```bash
 |cpu
      
      @0

         $reset = *reset;
         $pc[31:0] = >>1$reset ? 32'd0 :
                                      >>1$inc_pc[31:0];
      @1
         $inc_pc[31:0] = $pc[31:0] + 32'd4;



      // YOUR CODE HERE
      // ...

      // Note: Because of the magic we are using for visualisation, if visualisation is enabled below,
      //       be sure to avoid having unassigned signals (which you might be using for random inputs)
      //       other than those specifically expected in the labs. You'll get strange errors for these.

   
   // Assert these to end simulation (before Makerchip cycle limit).
   *passed = *cyc_cnt > 40;
   *failed = 1'b0;
   
   // Macro instantiations for:
   //  o instruction memory
   //  o register file
   //  o data memory
   //  o CPU visualization
   |cpu
      
      m4+imem(@1)    // Args: (read stage)
      //m4+rf(@1, @1)  // Args: (read stage, write stage) - if equal, no register bypass is required
      //m4+dmem(@4)    // Args: (read/write stage)

   m4+cpu_viz(@4)    // For visualisation, argument should be at least equal to the last stage of CPU logic. @4 would work for all labs.
\SV
   endmodule
```

## Result:
<img width="1918" height="817" alt="image" src="https://github.com/user-attachments/assets/61dc741b-53b6-465f-92d2-bd47de7dc6c4" />

## IMEMORY Modification
<img width="333" height="302" alt="image" src="https://github.com/user-attachments/assets/ba67e782-7f78-46a9-8bc0-603878458706" />

```bash
  |cpu
      @0
         $reset = *reset;

         $pc[31:0] = >>1$reset ? 32'd0 :
                                      >>1$inc_pc[31:0];
      @1
         $inc_pc[31:0] = $pc[31:0] + 32'd4;
      /imem[7:0]
         @0  
            $imem_rd_en = !$reset;
            $imem_rd_addr[M4_IMEM_INDEX_CNT-1:0]
                  = $pc[M4_IMEM_INDEX_CNT+1:2];
         @1 
            $instr[31:0] = $imem_rd_data[31:0];
```
## Result:
<img width="1919" height="821" alt="image" src="https://github.com/user-attachments/assets/ea27fce8-781d-4801-9a1e-af73e9045d06" />


## Decoder
<img width="1184" height="415" alt="image" src="https://github.com/user-attachments/assets/86f4a1f2-eb3a-425a-b0c8-90c66e7ec1a0" />

```bash
|cpu
      @0
         $reset = *reset;
         $pc[31:0] = >>1$reset ? 32'd0 :
                                      >>1$inc_pc[31:0];
      @1
         $inc_pc[31:0] = $pc[31:0] + 32'd4;
      /imem[7:0]
         @0  
            $imem_rd_en = !$reset;
            $imem_rd_addr[M4_IMEM_INDEX_CNT-1:0]
                  = $pc[M4_IMEM_INDEX_CNT+1:2];
         @1 
            $instr[31:0] = $imem_rd_data[31:0];
            
      /decode    
         @1
            $is_i_instr = $instr[6:2] ==? 5'b00_00x ||
                           $instr[6:2] ==? 5'b00_1x0 ||
                           $instr[6:2] ==? 5'b11_001 ;
            $is_r_instr = $instr[6:2] ==? 5'b01_011 ||
                           $instr[6:2] ==? 5'b01_1x0 ||
                           $instr[6:2] ==? 5'b10_100 ;
            $is_s_instr = $instr[6:2] ==? 5'b01_00x ;
            $is_b_instr = $instr[6:2] ==? 5'b11_000 ;
            $is_j_instr = $instr[6:2] ==? 5'b11_011 ;
            $is_u_instr = $instr[6:2] ==? 5'b0x_101 ;
```

## Result:
<img width="1918" height="815" alt="image" src="https://github.com/user-attachments/assets/79a66d45-caf5-4fe4-acac-330f2e5cea5e" />

## Decode forming imm [31:0] based on instruction type
<img width="1227" height="401" alt="image" src="https://github.com/user-attachments/assets/80fdd88c-6cd2-4922-924d-33c7cebfe4a6" />

```bash
|cpu
      @0
         $reset = *reset;
         $pc[31:0] = >>1$reset ? 32'd0 :
                                      >>1$inc_pc[31:0];
      @1
         $inc_pc[31:0] = $pc[31:0] + 32'd4;
      /imem[7:0]
         @0  
            $imem_rd_en = !$reset;
            $imem_rd_addr[M4_IMEM_INDEX_CNT-1:0]
                  = $pc[M4_IMEM_INDEX_CNT+1:2];
         @1 
            $instr[31:0] = $imem_rd_data[31:0];
            
      /decode
         
         @1
            $is_i_instr = $instr[6:2] ==? 5'b00_00x ||
                           $instr[6:2] ==? 5'b00_1x0 ||
                           $instr[6:2] ==? 5'b11_001 ;
            $is_r_instr = $instr[6:2] ==? 5'b01_011 ||
                           $instr[6:2] ==? 5'b01_1x0 ||
                           $instr[6:2] ==? 5'b10_100 ;
            $is_s_instr = $instr[6:2] ==? 5'b01_00x ;
            $is_b_instr = $instr[6:2] ==? 5'b11_000 ;
            $is_j_instr = $instr[6:2] ==? 5'b11_011 ;
            $is_u_instr = $instr[6:2] ==? 5'b0x_101 ;
            $imm[31:0] =
                  $is_i_instr ? { {21{$instr[31]}}, $instr[30:20] } :
            
                  $is_s_instr ? { {21{$instr[31]}},
                                   $instr[30:25],
                                   $instr[11:7] } :
            
                  $is_b_instr ? { {20{$instr[31]}},
                                   $instr[7],
                                   $instr[30:25],
                                   $instr[11:8],
                                   1'b0 } :
            
                  $is_u_instr ? { $instr[31:12],
                                   12'd0 } :
            
                  $is_j_instr ? { {12{$instr[31]}},
                                   $instr[19:12],
                                   $instr[20],
                                   $instr[30:21],
                                   1'b0 } :
            
                  32'b0;

```
## Result:
<img width="1919" height="820" alt="image" src="https://github.com/user-attachments/assets/9d86ee32-4a59-4339-a72e-78edf7a7d3ad" />

## In Decode Extract other instruction fields: $funct7, $funct3, $rs1, $rs2, $rd, $opcode
<img width="1124" height="465" alt="image" src="https://github.com/user-attachments/assets/e437ed55-5c91-4006-add9-cdcf98e77267" />

```bash

|cpu
      @0
         $reset = *reset;
         $pc[31:0] = >>1$reset ? 32'd0 :
                                      >>1$inc_pc[31:0];
      @1
         $inc_pc[31:0] = $pc[31:0] + 32'd4;
      /imem[7:0]
         @0  
            $imem_rd_en = !$reset;
            $imem_rd_addr[M4_IMEM_INDEX_CNT-1:0]
                  = $pc[M4_IMEM_INDEX_CNT+1:2];
         @1 
            $instr[31:0] = $imem_rd_data[31:0];
            
      /decode
         
         @1
            $is_i_instr = $instr[6:2] ==? 5'b00_00x ||
                           $instr[6:2] ==? 5'b00_1x0 ||
                           $instr[6:2] ==? 5'b11_001 ;
            $is_r_instr = $instr[6:2] ==? 5'b01_011 ||
                           $instr[6:2] ==? 5'b01_1x0 ||
                           $instr[6:2] ==? 5'b10_100 ;
            $is_s_instr = $instr[6:2] ==? 5'b01_00x ;
            $is_b_instr = $instr[6:2] ==? 5'b11_000 ;
            $is_j_instr = $instr[6:2] ==? 5'b11_011 ;
            $is_u_instr = $instr[6:2] ==? 5'b0x_101 ;
            $imm[31:0] =
                  $is_i_instr ? { {21{$instr[31]}}, $instr[30:20] } :
            
                  $is_s_instr ? { {21{$instr[31]}},
                                   $instr[30:25],
                                   $instr[11:7] } :
            
                  $is_b_instr ? { {20{$instr[31]}},
                                   $instr[7],
                                   $instr[30:25],
                                   $instr[11:8],
                                   1'b0 } :
            
                  $is_u_instr ? { $instr[31:12],
                                   12'd0 } :
            
                  $is_j_instr ? { {12{$instr[31]}},
                                   $instr[19:12],
                                   $instr[20],
                                   $instr[30:21],
                                   1'b0 } :
            
                  32'b0;

                    $funct7[6:0] = $instr[31:25];
                    
                    $rs2[4:0]    = $instr[24:20];
                    
                    $rs1[4:0]    = $instr[19:15];
                    
                    $funct3[2:0] = $instr[14:12];
                    
                    $rd[4:0]     = $instr[11:7];
                    
                    $opcode[6:0] = $instr[6:0];
```

## Result:
<img width="1919" height="826" alt="image" src="https://github.com/user-attachments/assets/59dc901c-1903-45a7-b403-65e374b5f888" />

## Updating $funct7, $funct3, $rs1, $rs2, $rd, $opcode with valid keyword
<img width="989" height="411" alt="image" src="https://github.com/user-attachments/assets/c38a6cc5-f367-4c6c-8502-7207b075ccec" />

```bash
 |cpu
      @0
         $reset = *reset;
         $pc[31:0] = >>1$reset ? 32'd0 :
                                      >>1$inc_pc[31:0];
      @1
         $inc_pc[31:0] = $pc[31:0] + 32'd4;
      /imem[7:0]
         @0  
            $imem_rd_en = !$reset;
            $imem_rd_addr[M4_IMEM_INDEX_CNT-1:0]
                  = $pc[M4_IMEM_INDEX_CNT+1:2];
         @1 
            $instr[31:0] = $imem_rd_data[31:0];
            
      /decode
         
         @1
            $is_i_instr = $instr[6:2] ==? 5'b00_00x ||
                           $instr[6:2] ==? 5'b00_1x0 ||
                           $instr[6:2] ==? 5'b11_001 ;
            $is_r_instr = $instr[6:2] ==? 5'b01_011 ||
                           $instr[6:2] ==? 5'b01_1x0 ||
                           $instr[6:2] ==? 5'b10_100 ;
            $is_s_instr = $instr[6:2] ==? 5'b01_00x ;
            $is_b_instr = $instr[6:2] ==? 5'b11_000 ;
            $is_j_instr = $instr[6:2] ==? 5'b11_011 ;
            $is_u_instr = $instr[6:2] ==? 5'b0x_101 ;
            
            $imm[31:0] = $is_i_instr ? { {21{$instr[31]}}, $instr[30:20] } :
            $is_s_instr ? { {21{$instr[31]}},
                             $instr[30:25],
                             $instr[11:7] } :

            $is_b_instr ? { {20{$instr[31]}},
                             $instr[7],
                             $instr[30:25],
                             $instr[11:8],
                             1'b0 } :

            $is_u_instr ? { $instr[31:12],
                             12'd0 } :

            $is_j_instr ? { {12{$instr[31]}},
                             $instr[19:12],
                             $instr[20],
                             $instr[30:21],
                             1'b0 } : 32'd0;


            $funct7_valid = $is_r_instr;
            ?$funct7_valid
               $funct7[6:0] = $instr[31:25];
            
            $rs2_valid = $is_r_instr || $is_s_instr || $is_b_instr;
            ?$rs2_valid
               $rs2[4:0]    = $instr[24:20];
              
            $rs1_valid = $is_r_instr || $is_i_instr || $is_s_instr || $is_b_instr;
            ?$rs2_valid
               $rs1[4:0]    = $instr[19:15];

            $funct3_valid = $is_r_instr || $is_i_instr || $is_s_instr || $is_b_instr;
            ?$funct3_valid
               $funct3[2:0] = $instr[14:12];

            $rd_valid = $is_r_instr || $is_i_instr || $is_u_instr || $is_j_instr;
            ?$rd_valid
               $rd[4:0]     = $instr[11:7];

            $opcode_valid = $is_r_instr || $is_i_instr || $is_s_instr || $is_b_instr || $is_u_instr || $is_j_instr;
            ?$opcode_valid
               $opcode[6:0] = $instr[6:0]; 

````

## Result:
<img width="1919" height="823" alt="image" src="https://github.com/user-attachments/assets/f7b826f0-e5bf-40ba-aaf4-b58c3e49933b" />

##  Decode RV32I Base Instruction Set
```bash
  |cpu
      @0
         $reset = *reset;
         $pc[31:0] = >>1$reset ? 32'd0 :
                                      >>1$inc_pc[31:0];
      @1
         $inc_pc[31:0] = $pc[31:0] + 32'd4;
      /imem[7:0]
         @0  
            $imem_rd_en = !$reset;
            $imem_rd_addr[M4_IMEM_INDEX_CNT-1:0]
                  = $pc[M4_IMEM_INDEX_CNT+1:2];
         @1 
            $instr[31:0] = $imem_rd_data[31:0];
            
      /decode
         
         @1
            $is_i_instr = $instr[6:2] ==? 5'b00_00x ||
                           $instr[6:2] ==? 5'b00_1x0 ||
                           $instr[6:2] ==? 5'b11_001 ;
            $is_r_instr = $instr[6:2] ==? 5'b01_011 ||
                           $instr[6:2] ==? 5'b01_1x0 ||
                           $instr[6:2] ==? 5'b10_100 ;
            $is_s_instr = $instr[6:2] ==? 5'b01_00x ;
            $is_b_instr = $instr[6:2] ==? 5'b11_000 ;
            $is_j_instr = $instr[6:2] ==? 5'b11_011 ;
            $is_u_instr = $instr[6:2] ==? 5'b0x_101 ;
            
            $imm[31:0] = $is_i_instr ? { {21{$instr[31]}}, $instr[30:20] } :
            $is_s_instr ? { {21{$instr[31]}},
                             $instr[30:25],
                             $instr[11:7] } :

            $is_b_instr ? { {20{$instr[31]}},
                             $instr[7],
                             $instr[30:25],
                             $instr[11:8],
                             1'b0 } :

            $is_u_instr ? { $instr[31:12],
                             12'd0 } :

            $is_j_instr ? { {12{$instr[31]}},
                             $instr[19:12],
                             $instr[20],
                             $instr[30:21],
                             1'b0 } : 32'd0;


            $funct7_valid = $is_r_instr;
            ?$funct7_valid
               $funct7[6:0] = $instr[31:25];
            
            $rs2_valid = $is_r_instr || $is_s_instr || $is_b_instr;
            ?$rs2_valid
               $rs2[4:0]    = $instr[24:20];
              
            $rs1_valid = $is_r_instr || $is_i_instr || $is_s_instr || $is_b_instr;
            ?$rs2_valid
               $rs1[4:0]    = $instr[19:15];

            $funct3_valid = $is_r_instr || $is_i_instr || $is_s_instr || $is_b_instr;
            ?$funct3_valid
               $funct3[2:0] = $instr[14:12];

            $rd_valid = $is_r_instr || $is_i_instr || $is_u_instr || $is_j_instr;
            ?$rd_valid
               $rd[4:0]     = $instr[11:7];

            $opcode_valid = $is_r_instr || $is_i_instr || $is_s_instr || $is_b_instr || $is_u_instr || $is_j_instr;
            ?$opcode_valid
               $opcode[6:0] = $instr[6:0];       
               
            
            $dec_bits[10:0] = {$funct7[5], $funct3, $opcode};
            
            $is_beq  = $dec_bits ==? 11'bx_000_1100011;

            $is_bne  = $dec_bits ==? 11'bx_001_1100011;

            $is_blt  = $dec_bits ==? 11'bx_100_1100011;

            $is_bge  = $dec_bits ==? 11'bx_101_1100011;

            $is_bltu = $dec_bits ==? 11'bx_110_1100011;

            $is_bgeu = $dec_bits ==? 11'bx_111_1100011;
            
            `BOGUS_USE($is_beq $is_bne $is_blt $is_bge $is_bltu $is_bgeu)

```
## Result:
<img width="1918" height="820" alt="image" src="https://github.com/user-attachments/assets/b84113fa-6a76-4173-add9-8c8a0e7abe78" />

## Register File Read
<img width="1421" height="513" alt="image" src="https://github.com/user-attachments/assets/5e268c9f-fbdf-4228-939c-876826260d41" />
<img width="888" height="442" alt="image" src="https://github.com/user-attachments/assets/a2bf9e9d-5f1e-447a-88f9-1c58e77ed1cc" />


```bash
|cpu
      @0
         $reset = *reset;
         $pc[31:0] = >>1$reset ? 32'd0 :
                                  >>1$inc_pc[31:0];

      @1
         $inc_pc[31:0] = $pc[31:0] + 32'd4;

      /imem[7:0]
         @0
            $imem_rd_en = !$reset;
            $imem_rd_addr[M4_IMEM_INDEX_CNT-1:0]
               = $pc[M4_IMEM_INDEX_CNT+1:2];

         @1
            $instr[31:0] = $imem_rd_data[31:0];

      /decode
         @1
            $is_i_instr = $instr[6:2] ==? 5'b00_00x ||
                          $instr[6:2] ==? 5'b00_1x0 ||
                          $instr[6:2] ==? 5'b11_001 ;

            $is_r_instr = $instr[6:2] ==? 5'b01_011 ||
                          $instr[6:2] ==? 5'b01_1x0 ||
                          $instr[6:2] ==? 5'b10_100 ;

            $is_s_instr = $instr[6:2] ==? 5'b01_00x ;
            $is_b_instr = $instr[6:2] ==? 5'b11_000 ;
            $is_j_instr = $instr[6:2] ==? 5'b11_011 ;
            $is_u_instr = $instr[6:2] ==? 5'b0x_101 ;

            $imm[31:0] =
                $is_i_instr ? {{21{$instr[31]}}, $instr[30:20]} :
                $is_s_instr ? {{21{$instr[31]}}, $instr[30:25], $instr[11:7]} :
                $is_b_instr ? {{20{$instr[31]}}, $instr[7], $instr[30:25],
                               $instr[11:8], 1'b0} :
                $is_u_instr ? {$instr[31:12], 12'd0} :
                $is_j_instr ? {{12{$instr[31]}}, $instr[19:12],
                               $instr[20], $instr[30:21], 1'b0} :
                               32'd0;

            $funct7_valid = $is_r_instr;
            ?$funct7_valid
               $funct7[6:0] = $instr[31:25];

            $rs2_valid = $is_r_instr || $is_s_instr || $is_b_instr;
            ?$rs2_valid
               $rs2[4:0] = $instr[24:20];

            $rs1_valid = $is_r_instr || $is_i_instr || $is_s_instr || $is_b_instr;
            ?$rs1_valid
               $rs1[4:0] = $instr[19:15];

            $funct3_valid = $is_r_instr || $is_i_instr ||
                            $is_s_instr || $is_b_instr;
            ?$funct3_valid
               $funct3[2:0] = $instr[14:12];

            $rd_valid = $is_r_instr || $is_i_instr ||
                        $is_u_instr || $is_j_instr;
            ?$rd_valid
               $rd[4:0] = $instr[11:7];

            $opcode_valid = $is_r_instr || $is_i_instr ||
                            $is_s_instr || $is_b_instr ||
                            $is_u_instr || $is_j_instr;
            ?$opcode_valid
               $opcode[6:0] = $instr[6:0];

            
            
      m4+rf(@1,@1)
      @1
         $rf_rd_en1 = $rs1_valid;
         $rf_rd_index1[4:0] = $rs1[4:0];

         $rf_rd_en2 = $rs2_valid;
         $rf_rd_index2[4:0] = $rs2[4:0];

````
## Result:
<img width="1919" height="820" alt="image" src="https://github.com/user-attachments/assets/84ff2167-c233-4a7d-8a77-8e624127a663" />

## Reg file o/p assign 
<img width="1473" height="494" alt="image" src="https://github.com/user-attachments/assets/b3776285-0194-48b1-bac1-9d0115c3e392" />

```bash
  m4+rf(@1,@1)
      @1
         $rf_rd_en1 = $rs1_valid;
         $rf_rd_index1[4:0] = $rs1[4:0];

         $rf_rd_en2 = $rs2_valid;
         $rf_rd_index2[4:0] = $rs2[4:0];
         
         //output
         
         $src1_value[31:0] = $rf_rd_data1[31:0];
         $src2_value[31:0] = $rf_rd_data2[31:0];
```
## Result:
<img width="1918" height="820" alt="image" src="https://github.com/user-attachments/assets/dd437be0-e825-49c4-abab-b72b2ebb7b33" />

## ALU
<img width="1473" height="494" alt="image" src="https://github.com/user-attachments/assets/339f95de-c3df-428a-b452-0f93d745e97b" />

```bash
/alu
         @1
            $result[31:0] = $is_addi ? ($src1_value + $imm) :
                            $is_add  ? ($src1_value + $src2_value) :
                            32'bx;

```
## Result:
<img width="1919" height="820" alt="image" src="https://github.com/user-attachments/assets/92824189-514c-4df6-8450-80014acc1e94" />

## Branches
<img width="1344" height="478" alt="image" src="https://github.com/user-attachments/assets/f59fed4a-6d83-4040-8dc4-8e6039372e30" />

``` bash
|cpu
      @0
         $reset = *reset;
         $pc[31:0] = >>1$reset ? 32'd0 :
                                  >>1$inc_pc[31:0];

      @1
         $inc_pc[31:0] = $pc[31:0] + 32'd4;
         
         $taken_br = $is_beq  ? ($src1_value == $src2_value) :
                       $is_bne  ? ($src1_value != $src2_value) :
                       $is_blt  ? (($src1_value <  $src2_value)^($src1_value[31] != $src2_value[31])) :
                       $is_bge  ? (($src1_value >=  $src2_value)^($src1_value[31] != $src2_value[31])) :
                       $is_bltu ? ($src1_value <  $src2_value) :
                       $is_bgeu ? ($src1_value >= $src2_value) :
                                                                1'b0;

      /imem[7:0]
         @0
            $imem_rd_en = !$reset;
            $imem_rd_addr[M4_IMEM_INDEX_CNT-1:0]
               = $pc[M4_IMEM_INDEX_CNT+1:2];

         @1
            $instr[31:0] = $imem_rd_data[31:0];

      /decode
         @1
            $is_i_instr = $instr[6:2] ==? 5'b00_00x ||
                          $instr[6:2] ==? 5'b00_1x0 ||
                          $instr[6:2] ==? 5'b11_001 ;

            $is_r_instr = $instr[6:2] ==? 5'b01_011 ||
                          $instr[6:2] ==? 5'b01_1x0 ||
                          $instr[6:2] ==? 5'b10_100 ;

            $is_s_instr = $instr[6:2] ==? 5'b01_00x ;
            $is_b_instr = $instr[6:2] ==? 5'b11_000 ;
            $is_j_instr = $instr[6:2] ==? 5'b11_011 ;
            $is_u_instr = $instr[6:2] ==? 5'b0x_101 ;

            $imm[31:0] =
                $is_i_instr ? {{21{$instr[31]}}, $instr[30:20]} :
                $is_s_instr ? {{21{$instr[31]}}, $instr[30:25], $instr[11:7]} :
                $is_b_instr ? {{20{$instr[31]}}, $instr[7], $instr[30:25],
                               $instr[11:8], 1'b0} :
                $is_u_instr ? {$instr[31:12], 12'd0} :
                $is_j_instr ? {{12{$instr[31]}}, $instr[19:12],
                               $instr[20], $instr[30:21], 1'b0} :
                               32'd0;

            $funct7_valid = $is_r_instr;
            ?$funct7_valid
               $funct7[6:0] = $instr[31:25];

            $rs2_valid = $is_r_instr || $is_s_instr || $is_b_instr;
            ?$rs2_valid
               $rs2[4:0] = $instr[24:20];

            $rs1_valid = $is_r_instr || $is_i_instr || $is_s_instr || $is_b_instr;
            ?$rs1_valid
               $rs1[4:0] = $instr[19:15];

            $funct3_valid = $is_r_instr || $is_i_instr ||
                            $is_s_instr || $is_b_instr;
            ?$funct3_valid
               $funct3[2:0] = $instr[14:12];

            $rd_valid = $is_r_instr || $is_i_instr ||
                        $is_u_instr || $is_j_instr;
            ?$rd_valid
               $rd[4:0] = $instr[11:7];

            $opcode_valid = $is_r_instr || $is_i_instr ||
                            $is_s_instr || $is_b_instr ||
                            $is_u_instr || $is_j_instr;
            ?$opcode_valid
               $opcode[6:0] = $instr[6:0];
            
            $dec_bits[10:0] = {$funct7[5], $funct3, $opcode};
            
            $is_beq  = $dec_bits ==? 11'bx_000_1100011;

            $is_bne  = $dec_bits ==? 11'bx_001_1100011;

            $is_blt  = $dec_bits ==? 11'bx_100_1100011;

            $is_bge  = $dec_bits ==? 11'bx_101_1100011;

            $is_bltu = $dec_bits ==? 11'bx_110_1100011;

            $is_bgeu = $dec_bits ==? 11'bx_111_1100011;
            
            `BOGUS_USE($is_beq $is_bne $is_blt $is_bge $is_bltu $is_bgeu)

            
            
      m4+rf(@1,@1)
      @1
         $rf_rd_en1 = $rs1_valid;
         $rf_rd_index1[4:0] = $rs1[4:0];

         $rf_rd_en2 = $rs2_valid;
         $rf_rd_index2[4:0] = $rs2[4:0];
         
         //output
         
         $src1_value[31:0] = $rf_rd_data1[31:0];
         $src2_value[31:0] = $rf_rd_data2[31:0];
         
         
               
      /alu
         @1
            $result[31:0] = $is_addi ? ($src1_value + $imm) :
                            $is_add  ? ($src1_value + $src2_value) :
                            32'bx;
         
            $rf_wr_en = $rd_valid && ($rd != 5'b00000);
            $rf_wr_index[4:0] = $rd[4:0];
            $rf_wr_data[31:0] = $result[31:0];

```
## Result:
<img width="1918" height="820" alt="image" src="https://github.com/user-attachments/assets/c784ccbd-9c4f-420a-b1da-7be14b8f143f" />

## Branch (Tacken Branch & Target Branch PC)
<img width="1312" height="461" alt="image" src="https://github.com/user-attachments/assets/01923c74-90a7-44d5-bd17-a25bf9f259e6" />

```bash
 |cpu
      @0
         $reset = *reset;
         $pc[31:0] = >>1$reset ? 32'd0 :
                     >>1$taken_br ? >>1$br_tgt_pc :
                                  >>1$inc_pc[31:0];
         

      @1
         $inc_pc[31:0] = $pc[31:0] + 32'd4;
         
         $taken_br = $is_beq  ? ($src1_value == $src2_value) :
                       $is_bne  ? ($src1_value != $src2_value) :
                       $is_blt  ? (($src1_value <  $src2_value)^($src1_value[31] != $src2_value[31])) :
                       $is_bge  ? (($src1_value >=  $src2_value)^($src1_value[31] != $src2_value[31])) :
                       $is_bltu ? ($src1_value <  $src2_value) :
                       $is_bgeu ? ($src1_value >= $src2_value) :
                                                                1'b0;
         //Target Branch Addition                                                       
         $br_tgt_bc[31:0] = $pc[31:0] + $imm[31:0];

      /imem[7:0]
         @0
            $imem_rd_en = !$reset;
            $imem_rd_addr[M4_IMEM_INDEX_CNT-1:0]
               = $pc[M4_IMEM_INDEX_CNT+1:2];

         @1
            $instr[31:0] = $imem_rd_data[31:0];

      /decode
         @1
            $is_i_instr = $instr[6:2] ==? 5'b00_00x ||
                          $instr[6:2] ==? 5'b00_1x0 ||
                          $instr[6:2] ==? 5'b11_001 ;

            $is_r_instr = $instr[6:2] ==? 5'b01_011 ||
                          $instr[6:2] ==? 5'b01_1x0 ||
                          $instr[6:2] ==? 5'b10_100 ;

            $is_s_instr = $instr[6:2] ==? 5'b01_00x ;
            $is_b_instr = $instr[6:2] ==? 5'b11_000 ;
            $is_j_instr = $instr[6:2] ==? 5'b11_011 ;
            $is_u_instr = $instr[6:2] ==? 5'b0x_101 ;

            $imm[31:0] =
                $is_i_instr ? {{21{$instr[31]}}, $instr[30:20]} :
                $is_s_instr ? {{21{$instr[31]}}, $instr[30:25], $instr[11:7]} :
                $is_b_instr ? {{20{$instr[31]}}, $instr[7], $instr[30:25],
                               $instr[11:8], 1'b0} :
                $is_u_instr ? {$instr[31:12], 12'd0} :
                $is_j_instr ? {{12{$instr[31]}}, $instr[19:12],
                               $instr[20], $instr[30:21], 1'b0} :
                               32'd0;

            $funct7_valid = $is_r_instr;
            ?$funct7_valid
               $funct7[6:0] = $instr[31:25];

            $rs2_valid = $is_r_instr || $is_s_instr || $is_b_instr;
            ?$rs2_valid
               $rs2[4:0] = $instr[24:20];

            $rs1_valid = $is_r_instr || $is_i_instr || $is_s_instr || $is_b_instr;
            ?$rs1_valid
               $rs1[4:0] = $instr[19:15];

            $funct3_valid = $is_r_instr || $is_i_instr ||
                            $is_s_instr || $is_b_instr;
            ?$funct3_valid
               $funct3[2:0] = $instr[14:12];

            $rd_valid = $is_r_instr || $is_i_instr ||
                        $is_u_instr || $is_j_instr;
            ?$rd_valid
               $rd[4:0] = $instr[11:7];

            $opcode_valid = $is_r_instr || $is_i_instr ||
                            $is_s_instr || $is_b_instr ||
                            $is_u_instr || $is_j_instr;
            ?$opcode_valid
               $opcode[6:0] = $instr[6:0];
            
            $dec_bits[10:0] = {$funct7[5], $funct3, $opcode};
            
            $is_beq  = $dec_bits ==? 11'bx_000_1100011;

            $is_bne  = $dec_bits ==? 11'bx_001_1100011;

            $is_blt  = $dec_bits ==? 11'bx_100_1100011;

            $is_bge  = $dec_bits ==? 11'bx_101_1100011;

            $is_bltu = $dec_bits ==? 11'bx_110_1100011;

            $is_bgeu = $dec_bits ==? 11'bx_111_1100011;
            
            `BOGUS_USE($is_beq $is_bne $is_blt $is_bge $is_bltu $is_bgeu)

            
            
      m4+rf(@1,@1)
      @1
         $rf_rd_en1 = $rs1_valid;
         $rf_rd_index1[4:0] = $rs1[4:0];

         $rf_rd_en2 = $rs2_valid;
         $rf_rd_index2[4:0] = $rs2[4:0];
         
         //output
         
         $src1_value[31:0] = $rf_rd_data1[31:0];
         $src2_value[31:0] = $rf_rd_data2[31:0];
         
         
               
      /alu
         @1
            $result[31:0] = $is_addi ? ($src1_value + $imm) :
                            $is_add  ? ($src1_value + $src2_value) :
                            32'bx;
         
            $rf_wr_en = $rd_valid && ($rd != 5'b00000);
            $rf_wr_index[4:0] = $rd[4:0];
            $rf_wr_data[31:0] = $result[31:0];

````
## Result:
<img width="1919" height="814" alt="image" src="https://github.com/user-attachments/assets/b9f51b8a-dffe-473a-876f-538133be41aa" />

