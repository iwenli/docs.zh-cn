---
title: ICorDebugThread::GetCurrentException 方法
ms.date: 03/30/2017
api_name:
- ICorDebugThread.GetCurrentException
api_location:
- mscordbi.dll
api_type:
- COM
f1_keywords:
- ICorDebugThread::GetCurrentException
helpviewer_keywords:
- ICorDebugThread::GetCurrentException method [.NET Framework debugging]
- GetCurrentException method [.NET Framework debugging]
ms.assetid: 331ed465-a195-4359-8584-b82c6098b29b
topic_type:
- apiref
ms.openlocfilehash: 3a4da6f958407c0b704ffb7372a77b7f022fc824
ms.sourcegitcommit: d6bd7903d7d46698e9d89d3725f3bb4876891aa3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/13/2020
ms.locfileid: "83379771"
---
# <a name="icordebugthreadgetcurrentexception-method"></a>ICorDebugThread::GetCurrentException 方法
获取一个指向 ICorDebugValue 对象的接口指针，该对象表示当前由托管代码引发的异常。  
  
## <a name="syntax"></a>语法  
  
```cpp  
HRESULT GetCurrentException (  
    [out] ICorDebugValue **ppExceptionObject  
);  
```  
  
## <a name="parameters"></a>参数  
 `ppExceptionObject`  
 弄指向对象地址的指针 `ICorDebugValue` ，该对象表示当前由托管代码引发的异常。  
  
## <a name="remarks"></a>备注  
 异常对象将在引发异常时出现，直至 `catch` 该块结束。 由 ICorDebugEval 方法执行的函数计算将清除安装程序上的异常对象并在完成时将其还原。  
  
 可以嵌套异常（例如，如果在筛选器或函数计算中引发了异常），则单个线程上可能存在多个未处理的异常。 `GetCurrentException`返回最新的异常。  
  
 异常对象和类型在异常的整个生命周期中可能会更改。 例如，在引发类型 x 的异常后，公共语言运行时（CLR）可能会耗尽内存，并将其提升为内存不足的异常。  
  
## <a name="requirements"></a>要求  
 **平台：** 请参阅[系统要求](../../get-started/system-requirements.md)。  
  
 **标头**：CorDebug.idl、CorDebug.h  
  
 **库：** CorGuids.lib  
  
 **.NET Framework 版本：**[!INCLUDE[net_current_v10plus](../../../../includes/net-current-v10plus-md.md)]
