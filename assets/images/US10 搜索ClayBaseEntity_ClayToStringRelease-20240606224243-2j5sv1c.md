#### 1. 定位函数 `VCFollowMove::MHFHCHOMGGJ`

   ```c
   // Namespace:MoleMole
   public class VCFollowMove : MCNMJHHHGMK, EKHPENKFFEP //TypeDefIndex: 34707
   {
        //以上略...
        //MethodIdx: 300327, RVA: 0x60d1480, VA: 0x1860d1480
	    protected bool MHFHCHOMGGJ() {}

        //以下略...
   }
   ```
#### 2. 在函数内定位 `VCFollowMove::CCGDGABMEAK`
   ```c
   // sub_1860D1480
__int64 __fastcall VCFollowMove::MHFHCHOMGGJ(__int64 pThis)
{
  unsigned int v1; // r14d
  __int64 v3; // rax
  __int64 v4; // rax
  __int64 v5; // rbx
  __int64 v6; // rax
  __int64 v7; // rcx
  __int64 v8; // rcx
  __int64 v9; // rcx
  __int64 v11; // rcx
  __int64 v12; // rdi
  __int64 v13; // rcx

  if ( !byte_1828F3000 )
  {
    if ( !*(_QWORD *)(pThis + 656) )
      clay::Exception::RaiseNullReferenceException_1();
    v3 = qword_1823BB240();
    v4 = sub_187872420(v3);
    LOBYTE(v1) = 1;
    if ( !v4 )
      return v1;
    v5 = v4;
    if ( *(_BYTE *)(qword_1823E2080 + 203) )
    {
      v6 = qword_1823C2F70;
      if ( !byte_1829097D1 )
      {
LABEL_6:
        v7 = *(_QWORD *)(v6 + 27792);
        if ( v7 && (unsigned __int8)sub_18B764890(v7, 3030i64, 0i64) )
          goto LABEL_8;
        goto LABEL_19;
      }
    }
    else
    {
      clay::vm::Runtime::ClassInit(qword_1823E2080);
      v6 = qword_1823C2F70;
      if ( !byte_1829097D1 )
        goto LABEL_6;
    }
    v11 = *(_QWORD *)(*(_QWORD *)(v6 + 136624) + 2545704i64);
    if ( !v11 )
      clay::Exception::RaiseNullReferenceException_1();
    if ( (unsigned __int8)sub_18A62C090(v11, 3030i64) )
    {
LABEL_8:
      if ( *(_BYTE *)(qword_1823EDF80 + 203) )
      {
        v8 = *(_QWORD *)(qword_1823C2F70 + 71568);
        if ( v8 )
        {
LABEL_10:
          v1 = 0;
          if ( !(unsigned __int8)sub_18B764890(v8, *(unsigned int *)(v5 + 940), 0i64) )
            return v1;
          // 0x2B0=>_chasedTargetRuntimeID_k__BackingField
          if ( *(_DWORD *)(pThis + 0x2B0) != *(_DWORD *)(v5 + 744) )
          {
            VCFollowMove::CCGDGABMEAK(pThis);   // <----定位此函数
            LOBYTE(v1) = 1;
            return v1;
          }
          return 0;
        }
      }
      else
      {
        clay::vm::Runtime::ClassInit(qword_1823EDF80);
        v8 = *(_QWORD *)(qword_1823C2F70 + 71568);
        if ( v8 )
          goto LABEL_10;
      }
      clay::Exception::RaiseNullReferenceException_1();
    }
LABEL_19:
    if ( *(_DWORD *)(pThis + 688) == *(_DWORD *)(v5 + 744) )
      return 0;
    *(_QWORD *)(pThis + 632) = 0i64;
    v12 = *(_QWORD *)qword_182493800;
    if ( (*(_BYTE *)(*(_QWORD *)qword_182493800 + 204i64) & 1) != 0 )
    {
      if ( *(_QWORD *)(*(_QWORD *)(v12 + 120) + 24i64) )
        goto LABEL_23;
    }
    else
    {
      clay::vm::Class::Init(*(_QWORD *)qword_182493800);
      if ( *(_QWORD *)(*(_QWORD *)(v12 + 120) + 24i64) )
        goto LABEL_23;
    }
    sub_18056B020(v12, 1i64);
LABEL_23:
    *(_OWORD *)(pThis + 640) = 0i64;
    sub_1860CADA0(pThis, 0i64, 0i64);
    if ( *(_BYTE *)(qword_1823EDF80 + 203) )
    {
      v13 = *(_QWORD *)(qword_1823C2F70 + 71568);
      if ( v13 )
        goto LABEL_25;
    }
    else
    {
      clay::vm::Runtime::ClassInit(qword_1823EDF80);
      v13 = *(_QWORD *)(qword_1823C2F70 + 71568);
      if ( v13 )
      {
LABEL_25:
        if ( (unsigned __int8)sub_18B764890(v13, *(unsigned int *)(v5 + 940), 0i64) )
          VCFollowMove::CCGDGABMEAK(pThis);
        return v1;
      }
    }
    clay::Exception::RaiseNullReferenceException_1();
  }
  v9 = *(_QWORD *)(*(_QWORD *)(qword_1823C2F70 + 136624) + 1808800i64);
  if ( !v9 )
    clay::Exception::RaiseNullReferenceException_1();
  return sub_18A5B7C10(v9, pThis);
}
   ```

#### 2. 在函数 `VCFollowMove::CCGDGABMEAK` 内定位 
```c
// sub_1860CF070
__int64 __fastcall VCFollowMove::CCGDGABMEAK(__int64 a1)
{
  unsigned int *v2; // rsi
  __int64 v3; // rdi
  __int64 v4; // rbx
  __int64 v5; // rcx
  __int64 result; // rax
  unsigned int v7; // ebx
  __int64 v8; // rcx
  __int64 v9; // rsi
  __int64 v10; // rcx
  __int64 v11; // rax
  __int64 v12; // rcx
  __int64 v13; // r12
  int v14; // edi
  __int64 v15; // rcx
  __int64 v16; // r15
  __int64 v17; // rcx
  __int64 v18; // r14
  __int64 v19; // rax
  __int64 v20; // rdx
  __int64 v21; // rcx
  __int64 v22; // r8
  __int64 v23; // rbx
  __int64 v24; // rdx
  __int64 v25; // rdx
  __int64 v26; // rcx
  __int64 v27; // r8
  __int64 *v28; // r12
  __int64 v29; // rdx
  __int64 v30; // rax
  __int64 v31; // rdx
  __int64 v32; // rdx
  __int64 v33; // rdx
  __int64 v34; // rcx
  __int64 *v35; // rdi
  __int64 v36; // r8
  __int64 v37; // rdx
  int v38; // eax
  __int64 v39; // rax
  __int64 v40; // rcx
  __int64 v41; // rdi
  __int64 v42; // rax
  int v43; // r8d
  __int64 v44; // rdi
  __int64 v45; // rax
  __int64 v46; // rcx
  int v47; // ecx
  __int64 v48; // rbx
  int *GenericMethod; // rcx
  __int64 v50; // rax
  __int64 v51; // rdx
  __int64 v52; // rdx
  __int64 v53; // rcx
  __int64 Method; // rax
  __int64 v55; // r8
  __int64 v56; // r8
  __int64 v57; // rax
  __int64 v58; // r8
  __int64 v59; // rax
  __int64 v60; // rax
  __int64 v61; // rax
  __int64 v62; // rax
  __int64 v63; // rax
  __int64 v64; // rax
  __int64 v65; // rax
  __int64 v66; // rax
  __int64 v67; // rax
  __int64 v68; // rax
  int v69; // [rsp+50h] [rbp-68h] BYREF
  int v70; // [rsp+54h] [rbp-64h] BYREF
  unsigned int *v71; // [rsp+58h] [rbp-60h] BYREF
  __int64 v72[11]; // [rsp+60h] [rbp-58h] BYREF

  if ( byte_1825E9AA0 )
  {
    if ( !byte_1828F2FEF )
      goto LABEL_3;
  }
  else
  {
    clay_mark_180573D60(0x5DE0u);
    byte_1825E9AA0 = 1;
    if ( !byte_1828F2FEF )
    {
LABEL_3:
      v2 = *(unsigned int **)(a1 + 40);
      v71 = v2;
      v3 = *(_QWORD *)qword_18247A850;
      if ( (*(_BYTE *)(*(_QWORD *)qword_18247A850 + 204i64) & 1) != 0 )
      {
        v4 = *(_QWORD *)(*(_QWORD *)(v3 + 120) + 16i64);
        if ( v4 )
          goto LABEL_5;
      }
      else
      {
        clay::vm::Class::Init(*(_QWORD *)qword_18247A850);
        v4 = *(_QWORD *)(*(_QWORD *)(v3 + 120) + 16i64);
        if ( v4 )
        {
LABEL_5:
          if ( (*(_BYTE *)(v4 + 204) & 1) != 0 )
            goto LABEL_6;
          goto LABEL_24;
        }
      }
      v11 = sub_18056B020(v3, 0i64);
      v4 = *(_QWORD *)v11;
      if ( (*(_BYTE *)(*(_QWORD *)v11 + 204i64) & 1) != 0 )
      {
LABEL_6:
        if ( v2 )
          goto LABEL_7;
        goto LABEL_25;
      }
LABEL_24:
      clay::vm::Class::Init(v4);
      if ( v2 )
      {
LABEL_7:
        v5 = **(_QWORD **)(v4 + 64);
        if ( !v5 )
          clay::Exception::RaiseNullReferenceException_1();
        result = sub_1872F03D0(v5, v2[186]);
        if ( !(_BYTE)result || v2[235] == 31 )
          return result;
        v7 = v2[187];
        if ( *(_BYTE *)(qword_1823EDF80 + 203) )
        {
          if ( !byte_1828F3002 )
            goto LABEL_13;
        }
        else
        {
          clay::vm::Runtime::ClassInit(qword_1823EDF80);
          if ( !byte_1828F3002 )
          {
LABEL_13:
            if ( *(_BYTE *)(qword_1823EDF80 + 203) )
            {
              v8 = *(_QWORD *)(qword_1823C2F70 + 71560);
              if ( v8 )
                goto LABEL_15;
            }
            else
            {
              clay::vm::Runtime::ClassInit(qword_1823EDF80);
              v8 = *(_QWORD *)(qword_1823C2F70 + 71560);
              if ( v8 )
              {
LABEL_15:
                if ( (unsigned __int8)sub_18B887620(v8, v7, 0i64) )
                  goto LABEL_16;
                goto LABEL_30;
              }
            }
            clay::Exception::RaiseNullReferenceException_1();
          }
        }
        v12 = *(_QWORD *)(*(_QWORD *)(qword_1823C2F70 + 136624) + 1808816i64);
        if ( !v12 )
          clay::Exception::RaiseNullReferenceException_1();
        if ( (unsigned __int8)sub_18A62C090(v12, v7) )
        {
LABEL_16:
          *(_QWORD *)(a1 + 0x278) = 0i64;       // _targetEntity
          v9 = *(_QWORD *)qword_182493800;
          if ( (*(_BYTE *)(*(_QWORD *)qword_182493800 + 204i64) & 1) != 0 )
          {
            if ( *(_QWORD *)(*(_QWORD *)(v9 + 120) + 24i64) )
            {
LABEL_18:
              *(_DWORD *)(a1 + 0x2B0) = 0;      // _chasedTargetRuntimeID_k__BackingField
              *(_OWORD *)(a1 + 0x280) = 0i64;   // _targetEntityHandle
              return sub_1860CADA0(a1, 0i64, 0i64);
            }
          }
          else
          {
            clay::vm::Class::Init(*(_QWORD *)qword_182493800);
            if ( *(_QWORD *)(*(_QWORD *)(v9 + 120) + 24i64) )
              goto LABEL_18;
          }
          sub_18056B020(v9, 1i64);
          goto LABEL_18;
        }
LABEL_30:
        result = (*(__int64 (__fastcall **)(unsigned int *, _QWORD))(*(_QWORD *)v2 + 1184i64))(v2, 0i64);
        if ( !(_BYTE)result )
          return result;
        v13 = MoleMole_BaseEntity_ToStringRelease(v2);
        v14 = v2[187];
        v15 = *(_QWORD *)(a1 + 0x278);
        if ( v15 )
        {
          v16 = MoleMole_BaseEntity_ToStringRelease(v15);//  <-- 定位此函数
          v17 = *(_QWORD *)(a1 + 656);
          if ( !v17 )
            goto LABEL_37;
        }
        else
        {
          v16 = qword_18257D948;
          v17 = *(_QWORD *)(a1 + 656);
          if ( !v17 )
            goto LABEL_37;
        }
        if ( *(_QWORD *)(v17 + 16) )
        {
          v18 = qword_1823BE148();
LABEL_38:
          v19 = sub_180553C10(qword_1823D1FF0, 5i64);
          if ( !v19 )
            clay::Exception::RaiseNullReferenceException_1();
          v23 = v19;
          if ( v13 )
          {
            v24 = *(_DWORD *)(*(_QWORD *)v19 + 168i64)
                ? *(_QWORD *)utils_s_Callbacks + *(unsigned int *)(*(_QWORD *)v19 + 168i64)
                : 0i64;
            if ( !sub_180579A20(v13, v24) )
            {
              v64 = sub_1805E7C20();
              sub_1805E7B90(v64, 0i64);
            }
          }
          if ( !*(_DWORD *)(v23 + 24) )
          {
            v59 = sub_18056C800(v21, v20, v22);
            sub_1805E7B90(v59, 0i64);
          }
          *(_QWORD *)(v23 + 32) = v13;
          v70 = v14;
          v28 = clay::vm::Object::Box(qword_1823D26E8, (char *)&v70);
          if ( v28 )
          {
            v29 = *(_DWORD *)(*(_QWORD *)v23 + 168i64)
                ? *(_QWORD *)utils_s_Callbacks + *(unsigned int *)(*(_QWORD *)v23 + 168i64)
                : 0i64;
            if ( !sub_180579A20(v28, v29) )
            {
              v65 = sub_1805E7C20();
              sub_1805E7B90(v65, 0i64);
            }
          }
          v30 = *(_QWORD *)(v23 + 24);
          if ( (v30 & 0xFFFFFFFE) == 0 )
          {
            v60 = sub_18056C800(v26, v25, v27);
            sub_1805E7B90(v60, 0i64);
          }
          *(_QWORD *)(v23 + 40) = v28;
          if ( v16 )
          {
            if ( *(_DWORD *)(*(_QWORD *)v23 + 168i64) )
              v31 = *(_QWORD *)utils_s_Callbacks + *(unsigned int *)(*(_QWORD *)v23 + 168i64);
            else
              v31 = 0i64;
            if ( !sub_180579A20(v16, v31) )
            {
              v66 = sub_1805E7C20();
              sub_1805E7B90(v66, 0i64);
            }
            v30 = *(_QWORD *)(v23 + 24);
          }
          if ( (unsigned int)v30 <= 2 )
          {
            v61 = sub_18056C800(v26, v25, v27);
            sub_1805E7B90(v61, 0i64);
          }
          *(_QWORD *)(v23 + 48) = v16;
          if ( v18 )
          {
            if ( *(_DWORD *)(*(_QWORD *)v23 + 168i64) )
              v32 = *(_QWORD *)utils_s_Callbacks + *(unsigned int *)(*(_QWORD *)v23 + 168i64);
            else
              v32 = 0i64;
            if ( !sub_180579A20(v18, v32) )
            {
              v67 = sub_1805E7C20();
              sub_1805E7B90(v67, 0i64);
            }
            v30 = *(_QWORD *)(v23 + 24);
          }
          if ( (v30 & 0xFFFFFFFC) == 0 )
          {
            v62 = sub_18056C800(v26, v25, v27);
            sub_1805E7B90(v62, 0i64);
          }
          *(_QWORD *)(v23 + 56) = v18;
          v69 = *(_DWORD *)(a1 + 688);
          v35 = clay::vm::Object::Box(qword_1823D26E8, (char *)&v69);
          if ( v35 )
          {
            v37 = *(_DWORD *)(*(_QWORD *)v23 + 168i64)
                ? *(_QWORD *)utils_s_Callbacks + *(unsigned int *)(*(_QWORD *)v23 + 168i64)
                : 0i64;
            if ( !sub_180579A20(v35, v37) )
            {
              v68 = sub_1805E7C20();
              sub_1805E7B90(v68, 0i64);
            }
          }
          v38 = *(_DWORD *)(v23 + 24);
          if ( (unsigned int)v38 <= 4 )
          {
            v63 = sub_18056C800(v34, v33, v36);
            sub_1805E7B90(v63, 0i64);
          }
          *(_QWORD *)(v23 + 64) = v35;
          if ( v38 <= 0 )
          {
            v39 = 0i64;
            v40 = 0i64;
            v41 = 0i64;
          }
          else
          {
            v39 = *(_QWORD *)(v23 + 32);
            v40 = *(_QWORD *)(v23 + 40);
            v41 = *(_QWORD *)(v23 + 48);
          }
          v72[0] = v39;
          v72[1] = v40;
          v72[2] = v41;
          v72[3] = v23;
          v42 = System::String::FormatHelper(0i64, qword_1825A51F0, v72);
          if ( !v42 )
            LODWORD(v42) = qword_182596F10;
          LOBYTE(v43) = 1;
          sub_18BD09510(0, v42, v43, 0, 2, 11, qword_18257E750, 1, 0, qword_18257E750);
          v44 = qword_18247A770;
          v45 = *(unsigned __int16 *)(qword_18247A770 + 46);
          v46 = *(_QWORD *)v2;
          if ( (*(_BYTE *)(*(_QWORD *)v2 + 205i64) & 0x10) != 0 )
          {
            v53 = *(_QWORD *)(v46 + 8 * (*(unsigned __int16 *)(v46 + 194) + v45) + 208);
          }
          else
          {
            v47 = *(_DWORD *)(s_GlobalMetadata
                            + (*(int *)(s_GlobalMetadataHeader + 44) ^ 0x26CF6BA4i64)
                            + 4i64 * ((int)v45 + *(_DWORD *)(*(_QWORD *)(v46 + 32) + 12i64) - 2008458702));
            v48 = v47 & 0x1FFFFFFF;
            if ( (v47 & 0x1FFFFFFF) != 0 )
            {
              if ( (v47 & 0xE0000000) == -1073741824 )
              {
                GenericMethod = *(int **)(s_GenericMethodTable + 8 * v48);
                if ( !GenericMethod )
                {
                  v50 = *(_DWORD *)(s_GlobalMetadataHeader + 152) - 1889790466 + s_GlobalMetadata;
                  v51 = *(unsigned __int16 *)(v50 + 8 * v48 + 6);
                  if ( v51 == 0xFFFF )
                    v52 = 0i64;
                  else
                    v52 = *(_QWORD *)(s_Il2CppMetadataRegistration + 56) + 16 * v51;
                  v55 = *(unsigned __int16 *)(v50 + 8 * v48 + 4);
                  if ( v55 == 0xFFFF )
                    v56 = 0i64;
                  else
                    v56 = *(_QWORD *)(s_Il2CppMetadataRegistration + 56) + 16 * v55;
                  GenericMethod = (int *)clay::vm::MetadataCache::GetGenericMethod(*(_DWORD *)(v50 + 8 * v48), v52, v56);
                  *(_QWORD *)(s_GenericMethodTable + 8 * v48) = GenericMethod;
                }
                Method = (__int64)clay::GenericMethod::GetMethod(GenericMethod, 0);
              }
              else
              {
                Method = clay::vm::MetadataCache::GetMethodInfoFromMethodDefinitionIndex(v48);
              }
              v53 = Method;
            }
            else
            {
              v53 = 0i64;
            }
          }
          v57 = sub_180587190(v53, v44);
          LOBYTE(v58) = 1;
          return (*(__int64 (__fastcall **)(unsigned int *, unsigned int **, __int64, _QWORD, __int64))(v57 + 8))(
                   v2,
                   &v71,
                   v58,
                   0i64,
                   v57);
        }
LABEL_37:
        v18 = qword_18257D948;
        goto LABEL_38;
      }
LABEL_25:
      clay::Exception::RaiseNullReferenceException_1();
    }
  }
  v10 = *(_QWORD *)(*(_QWORD *)(qword_1823C2F70 + 136624) + 1808664i64);
  if ( !v10 )
    clay::Exception::RaiseNullReferenceException_1();
  return sub_18A8DE8D0(v10, a1);
}
```

```c
// sub_1884B3120
__int64 __fastcall MoleMole_BaseEntity_ToStringRelease(__int64 a1)
{
    //省略...
}
```