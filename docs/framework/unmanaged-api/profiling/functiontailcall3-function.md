---
title: Функция FunctionTailcall3
ms.date: 03/30/2017
api_name:
- FunctionTailcall3
api_location:
- mscorwks.dll
api_type:
- COM
f1_keywords:
- FunctionTailcall3
helpviewer_keywords:
- FunctionTailcall3 function [.NET Framework profiling]
ms.assetid: 1e48243f-5de6-4bd6-a1d0-e1d248bca4b8
topic_type:
- apiref
author: mairaw
ms.author: mairaw
ms.openlocfilehash: 82d718c6f6aee36f3a916eb7f9b9a1e0b3b46cb5
ms.sourcegitcommit: 3d5d33f384eeba41b2dff79d096f47ccc8d8f03d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2018
ms.locfileid: "33452472"
---
# <a name="functiontailcall3-function"></a>Функция FunctionTailcall3
Уведомляет профилировщик о том, что текущей выполняемой функции собирается выполнить вызов с префиксом tail в другую функцию.  
  
## <a name="syntax"></a>Синтаксис  
  
```  
void __stdcall FunctionTailcall3 (FunctionOrRemappedID functionOrRemappedID);  
```  
  
#### <a name="parameters"></a>Параметры  
 `functionOrRemappedID`  
 [in] Идентификатор текущей выполняемой функции, внесет с префиксом tail.  
  
## <a name="remarks"></a>Примечания  
 `FunctionTailcall3` Функция обратного вызова Уведомляет профилировщик при вызове функций. Используйте [метод ICorProfilerInfo3::SetEnterLeaveFunctionHooks3](../../../../docs/framework/unmanaged-api/profiling/icorprofilerinfo3-setenterleavefunctionhooks3-method.md) для регистрации реализации этой функции.  
  
 `FunctionTailcall3` Функция является обратным вызовом, необходимо произвести его. В реализации должен использоваться `__declspec(naked)` атрибут класса хранения.  
  
 Перед вызовом этой функции ядро выполнения не сохраняет значения регистров.  
  
-   При входе необходимо сохранить все используемые регистры, включая те, в единицах измерения с плавающей запятой (FPU).  
  
-   При выходе необходимо восстановить стек путем выталкивания из всех параметров, переведенных вызывающим кодом.  
  
 Реализация `FunctionTailcall3` не должен блокироваться, поскольку это приведет к задержке сборки мусора. Реализация не должны предпринимать попытки сбора мусора, так как стек может находиться в состоянии понятного имени сбора мусора. При попытке сбора мусора, среда выполнения будет блокироваться до `FunctionTailcall3` возвращает.  
  
 `FunctionTailcall3` Функция не должна вызывать управляемый код или вызвать распределения управляемой памяти каким-либо образом.  
  
## <a name="requirements"></a>Требования  
 **Платформы:** разделе [требования к системе для](../../../../docs/framework/get-started/system-requirements.md).  
  
 **Заголовок:** CorProf.idl  
  
 **Библиотека:** CorGuids.lib  
  
 **Версии платформы .NET framework:** [!INCLUDE[net_current_v40plus](../../../../includes/net-current-v40plus-md.md)]  
  
## <a name="see-also"></a>См. также  
 [FunctionEnter3](../../../../docs/framework/unmanaged-api/profiling/functionenter3-function.md)  
 [FunctionLeave3](../../../../docs/framework/unmanaged-api/profiling/functionleave3-function.md)  
 [FunctionEnter3WithInfo](../../../../docs/framework/unmanaged-api/profiling/functionenter3withinfo-function.md)  
 [FunctionLeave3WithInfo](../../../../docs/framework/unmanaged-api/profiling/functionleave3withinfo-function.md)  
 [Функция FunctionTailcall3WithInfo](../../../../docs/framework/unmanaged-api/profiling/functiontailcall3withinfo-function.md)  
 [SetEnterLeaveFunctionHooks3](../../../../docs/framework/unmanaged-api/profiling/icorprofilerinfo3-setenterleavefunctionhooks3-method.md)  
 [SetEnterLeaveFunctionHooks3WithInfo](../../../../docs/framework/unmanaged-api/profiling/icorprofilerinfo3-setenterleavefunctionhooks3withinfo-method.md)  
 [SetFunctionIDMapper](../../../../docs/framework/unmanaged-api/profiling/icorprofilerinfo-setfunctionidmapper-method.md)  
 [SetFunctionIDMapper2](../../../../docs/framework/unmanaged-api/profiling/icorprofilerinfo3-setfunctionidmapper2-method.md)  
 [Глобальные статические функции профилирования](../../../../docs/framework/unmanaged-api/profiling/profiling-global-static-functions.md)
