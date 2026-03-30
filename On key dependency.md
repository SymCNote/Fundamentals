

例子 (SKINNY):

'''
SKINNY_SB_4 = [12,6,9,0,1,10,2,11,3,8,5,13,4,14,7,15]
Diff_d2, Diff_d9, Diff_bc, Diff_25 = (0xd,0x2), (0xd,0x9), (0xb,0xc), (0x2, 0x5)

# '''
# The half constraint:
# * x_DDT is the -input- value of Sbox which fulfills the differential
# * y_DDT is the -output- value of Sbox which fulfills the differential
# '''

y_DDT_d2 = {SKINNY_SB_4[x] for x in range(16) if SKINNY_SB_4[x] ^ SKINNY_SB_4[x^Diff_d2[0]] == Diff_d2[1]}
y_DDT_d9 = {SKINNY_SB_4[x] for x in range(16) if SKINNY_SB_4[x] ^ SKINNY_SB_4[x^Diff_d9[0]] == Diff_d9[1]}
x_DDT_bc = {x for x in range(16) if SKINNY_SB_4[x] ^ SKINNY_SB_4[x^Diff_bc[0]] == Diff_bc[1]}
x_DDT_25 = {x for x in range(16) if SKINNY_SB_4[x] ^ SKINNY_SB_4[x^Diff_25[0]] == Diff_25[1]}


subkey_02_C1 = {(v1 ^ v2 ^ 0x2 ^ v3) for v1 in y_DDT_d2 for v2 in y_DDT_d9 for v3 in x_DDT_bc} & set(range(16))
subkey_02_C2 = {(v1 ^ 0x2 ^ v2) for v1 in y_DDT_d2 for v2 in x_DDT_25} & set(range(16))


print("y_DDT_d2 :", [f"{v:02x}" for v in sorted(y_DDT_d2)])
print("y_DDT_d9 :", [f"{v:02x}" for v in sorted(y_DDT_d9)])
print("x_DDT_bc :", [f"{v:02x}" for v in sorted(x_DDT_bc)])
print("x_DDT_25 :", [f"{v:02x}" for v in sorted(x_DDT_bc)])
print("subkey_02_C1:", [f"{k:02x}" for k in sorted(subkey_02_C1)])
print("subkey_02_C1:", [f"{k:02x}" for k in sorted(subkey_02_C2)])

inter_C = subkey_02_C1.intersection(subkey_02_C2)

print("intersection of C1 and C2:",inter_C)
'''
