# **晶片金融卡功能流程** *****`Chip Financial Card Function Process`*****

- [**晶片金融卡功能流程** *****`Chip Financial Card Function Process`*****](#晶片金融卡功能流程-chip-financial-card-function-process)
  - [專案介紹 ── Description](#專案介紹--description)
  - [運行環境需求 ── Requirement](#運行環境需求--requirement)
    - [WebATM元件API ── Main API](#webatm元件api--main-api)
    - [前端-敘述 ── Frontend Description](#前端-敘述--frontend-description)
    - [後端-敘述 ── Backend Description](#後端-敘述--backend-description)
  - [功能流程 ── Functional Flow](#功能流程--functional-flow)
    - [使用者操作手冊 ── User Manual](#使用者操作手冊--user-manual)


## [專案介紹 ── Description](#晶片金融卡功能流程-chip-financial-card-function-process)
此專案為網銀功能中的晶片金融卡功能流程，使用前需先行下載並安裝特定的安控元件，運行成功後，可使用此功能流程，完成讀卡機的操作。

功能流程

![圖片1](https://github.com/ZhongYah/Project_Chip_Financial_Card/assets/104511360/b75ec9f3-462e-4739-b073-d1b259dfd41e)


功能示意圖

![image](https://github.com/ZhongYah/Project_Chip_Financial_Card/assets/104511360/b70aec84-29b6-4520-a99c-5d659d269210)


## [運行環境需求 ── Requirement](#晶片金融卡功能流程-chip-financial-card-function-process)
>前端（Frontend）：React、JavaScript、Node.js

>後端（Backend）：Java、Spring Boot
### [WebATM元件API ── Main API](#晶片金融卡功能流程-chip-financial-card-function-process)


(1)若為Windows環境則載入`SCSBCGWebATMSVIAdapter.js`、若為MAC OS X環境則載入`SCSBWebATMwxAdapter.js`。

(2)載入後宣告對應的物件名稱。(例如若為Windows環境則宣告getSCSBCGWebATMSVIAdapterObj ()、若為MAC OS X環境則宣告getSCSBWebATMwxAdapterObj())。
### [前端-敘述 ── Frontend Description](#前端-敘述--frontend-description) 
`useSCSBWebATM`

該`Hook`是整合WebATM安控軟體，提供了多個功能來執行與智慧卡相關的操作。


>其安控軟體API手冊「WebATM安控軟體(WebATM元件).docx」   
>[WebATM元件.docx](https://www.google.com/)

使用方法就如一般的自訂 `React Hook`，總計 10 種功能

|  No.  |          Name           |   Type   |                   Purpose                   |
| :---: | :---------------------: | :------: | :-----------------------------------------: |
|   1   | isSCSBWebATMInitialized | boolean  | 表示 SCSBWebATM 是否資源已經初始化的標記。  |
|   2   |  initSCSBWebATMAdapter  | function |      初始化 SCSBWebATMAdapter 的函數。      |
|   3   |         getCode         | function |            獲取錯誤代碼的函數。             |
|   4   |       connectCard       | function |              連結卡片的函數。               |
|   5   |     disconnectCard      | function |             中斷與卡片的連結。              |
|   6   |      getReaderList      | function |           獲取讀卡機列表的函數。            |
|   7   |       getVersion        | function |         取得底層元件版本號的函數。          |
|   8   |      getCardStatus      | function |            取得卡片狀態的函數。             |
|   9   |     getSCSBAccount      | function |         得到持卡人基本資料的函數。          |
|  10   |        verifyPIN        | function | 傳送 PIN 碼至晶片金融卡進行密碼驗證的函數。 |

交易開發常用功能為:  
`No.1`、`No.3`、`No.6`、`No.8`、`No.9`、`No.10`  
其餘為組件內部自控或擴充性提供之功能。  

在`Hook`提供的功能函式中，有分為同步/異步函數 :  
- 同步（Synchronous）:  
`No.2`、`No.3`、`No.5`、`No.6`、`No.7`  
- 異步（Asynchronous）:  
`No.4`、`No.8`、`No.9`、`No.10`  

而除了`No.2`、`No.3`以外，  
所有的功能函式都會回傳一個統一結構的物件，  
物件包含了`code`和`data`屬性，
> `code`代表錯誤代碼，如果為 `0` 表示成功，其他代碼可查照API手冊。  
> `data`代表回傳的資料。
> 
> 回傳的資料則視函數結果可能會是`undefined`、`string`、`array`等型態，  
> 各個函數註解中皆會標記清楚，可查閱手冊。

<br/>
    
各功能介紹，代碼也有提供 `JSDoc` 註解:  

- getCode - 獲取錯誤代碼的函數。
> 取得元件API回傳碼
> 常見代碼如下，其他代碼可查照API手冊。   
> `0`:     執行成功。   
> `5908`:  不被允許使用本元件。    
> `61001`: ServiSign 一般性錯誤(未正常啟動ServiSign主程式)。    
> `5070`:  使用者取消操作。  
> `5071`:	密碼不正確。  
> `3020`:  表示密碼規則長度有問題。  
> `61203`: ServiSign 版本錯誤。
> `61006`: 無法與ServiSign建立連線。

- isSCSBWebATMInitialized - 表示 SCSBWebATM 是否資源已經初始化的標記。
> 組件初次渲染時初始化 SCSBWebATM 元件。  
> 如果需要在 SCSBWebATM 元件資源初始化完成判斷狀態代碼時，  
> 可以搭配 `useEffect` Hook 和 `getCode` 函式
> ```javascript
> useEffect(() => {
>   if (isSCSBWebATMInitialized) {
>     console.log('useEffect getCode', getCode());
>   }
> }, [getCode, isSCSBWebATMInitialized]);
> ```

- initSCSBWebATMAdapter - 初始化 SCSBWebATMAdapter 的函數。
> 用來初始化 SCSB WebATM，基本上`Hook`內部會自控，也提供該功能使用。 

- connectCard - 連結卡片的函數。
> 連結卡片，基本上`Hook`內部會自控，也提供該功能使用。 

- disconnectCard - 中斷與卡片的連結。
> 中斷與卡片的連結，基本上`Hook`內部會自控，也提供該功能使用。

- getReaderList - 獲取讀卡機列表的函數。
> 獲取讀卡機列表  
> 回傳的資料是字串陣列，裡面會是讀卡機名稱  
> 可以將其資料經過處理作為下拉式選單來源，  
> 交易控制選擇之讀卡機名稱，再以此操控其餘功能。  

- getVersion - 取得底層元件版本號的函數。
> 取得底層元件版本號，提供該功能使用。

- getCardStatus - 傳入讀卡機名稱，取得卡片狀態的函數。
> 輸出代碼，其他代碼請查照API手冊  
> 0:     已插入。  
> 3011: 未接上任何讀卡機。  
> 3005: 卡片不存在、卡片異常或無法判別的卡片。  
> 3999: 一般性錯誤未插入。  
> 
> 經測試，假如故意帶入不存在之讀卡機名稱，則會返回 3005

- getSCSBAccount - 傳入讀卡機名稱，得到持卡人基本資料的函數。
> 得到持卡人基本資料。
> 
> 卡片檔案資料數組內容順序:  
> - 發卡單位代號(長度8)
> - 晶片卡備註欄(長度30)
> - 帳號1(長度16)
> - 帳號2(長度16)
> - 帳號3(長度16)
> - 帳號4(長度16)
> - 帳號5(長度16)
> - 帳號6(長度16)
> - 帳號7(長度16)
> - 帳號8(長度16)

- verifyPIN - 傳送 讀卡機名稱 和 PIN 碼至晶片金融卡進行密碼驗證的函數。
> 若`PIN`帶空值 + 一代讀卡機，則由元件跳出畫面輸入。  
> 若`PIN`帶空值 + 二代讀卡機，則由讀卡機鍵盤輸入。  
> 若`PIN`有帶入值 + 一代讀卡機，元件將值直接帶入卡片進行驗證。  
> 若`PIN`有帶入值 + 二代讀卡機，則元件將值帶入卡片後，使用者需再於讀卡機上按下確認鍵。 

### [後端-敘述 ── Backend Description](#晶片金融卡功能流程-chip-financial-card-function-process) 
>前端會先 call 後端的API (`"/verifyServiSign"`)，後端在 call 全景的server，執行雙向驗證，驗證成功後，前端方可使用安控元件以及晶片卡功能流程。

## [功能流程 ── Functional Flow](#晶片金融卡功能流程-chip-financial-card-function-process)

>### [引用 Custom Hooks ── Import](#晶片金融卡功能流程-chip-financial-card-function-process)
>Custom Hooks { `useSCSBWebATM` }，該 Hook 提供了多個功能來執行與智慧卡相關的操作。
>```javaScript
>    const {
>        isSCSBWebATMInitialized,
>        initSCSBWebATMAdapter,
>        getCode,
>        connectCard,
>        disconnectCard,
>        getReaderList,
>        getCardStatus,
>        getSCSBAccount,
>        verifyPIN
>    } = useSCSBWebATM();
>```

<br/>

>### [引用 Custom Utils ── Import](#晶片金融卡功能流程-chip-financial-card-function-process)
> 根據晶片金融卡錯誤狀態碼取得對應的 i18n 鍵值。
>* @param {number} code - 晶片金融卡錯誤狀態碼。
>* @returns {string} 對應的 i18n 鍵值。
>```javaScript
>getChipCardErrorKeyByCode(code)
>```

<br/>

>### [定義錯誤訊息燈箱 ── Function](#晶片金融卡功能流程-chip-financial-card-function-process)
>為個別交易的共用錯誤訊息燈箱，搭配 `getChipCardErrorKeyByCode(code)` 使用。
>```javaScript
>// 錯誤燈箱
>const handleOpenErrorModal = useCallback((title, children) => {
>        // code: 61001、61006、5908 為安控元件異常時，常見的錯誤代碼，若為>其一，則跳下載安控元件的燈箱
>        const type = (children === 61001) || (children === 61006) || (children === 5908);
>        // 燈箱內容 Button
>        const modalContent = (
>            <Fragment>
>                {type && <div className='col-md-auto'>
>                    <Button
>                        text={translate('COMPONENT.BUTTON.CANCEL')}
>                        onClick={() => {
>                            handleClick('CANCEL');
>                        }}
>                    />
>                </div>}
>                {type && <div className='col-md-auto'>
>                    <GradientButton
>                        text={translate('COMPONENT.BUTTON.DOWNLOAD')}
>                        onClick={() => {
>                            handleClick('DOWNLOAD');
>                        }}
>                    />
>                </div>}
>                {!type && <div className='col-md-auto'>
>                    <GradientButton
>                        onClick={() => {
>                            dispatch({ type: 'CLOSE_MODAL' });
>                        }}
>                        text={translate('COMPONENT.BUTTON.CONFIRM')}
>                    />
>                </div>}
>            </Fragment>
>        );
>
>       dispatch({
>            type: 'OPEN_MODAL',
>            payload: {
>                title: translate(title),
>                children: isString(children) ? translate(children) : (isNumber(children) || isNaN(children)) ? translate(getChipCardErrorKeyByCode(children)) : translate(children),
>                showCloseBtn: false,
>                upperRightCloseBtn: false,
>                backdropStatic: true,
>                customContent: modalContent,
>            },
>        });
>    }, [handleClick, translate]);
>```
<br/>

>### [可確認安控元件狀態的函數 ── Function](#晶片金融卡功能流程-chip-financial-card-function-process)
>呼叫這個函數，可以 call API (`"/verifyServiSign"`)執行安控元件的初始化驗證，可用來判段安控元件是否正常運作。
>```javaScript
>    const handleInitSCSBWebATMAdapter = useCallback(() => {
>        const code = initSCSBWebATMAdapter();
>        // code不為0，代表執行不成功
>        if (code !== 0) {
>            handleOpenErrorModal('ERROR.MESSAGE', code);
>        }
>    }, [handleOpenErrorModal, initSCSBWebATMAdapter]);
>```

<br/>

>### [定義中斷卡片連結的函數 ── Function](#晶片金融卡功能流程-chip-financial-card-function-process)     
>呼叫此函數，以確保卡片中斷連結
>```javaScript
>    const handleDisconnect = useCallback(() => {
>        const disconnect = async () => {
>            const { code } = await disconnectCard();
>            // code不為0，代表執行不成功
>            if (code !== 0) {
>                handleOpenErrorModal('ERROR.MESSAGE', code);
>            }
>        };
>        disconnect();
>    }, [disconnectCard, handleOpenErrorModal]);
>```

<br/>

>### [一進到頁面，初始化安控元件狀態 ── Process 1](#晶片金融卡功能流程-chip-financial-card-function-process) 
>```javascript
>    // 一進到頁面，初始化安控元件狀態
>    useEffect(() => {
>        // 執行晶片金融卡元件
>        const fetchCode = async () => {
>            // isSCSBWebATMInitialized 作為初始化依據
>            if (isSCSBWebATMInitialized) {
>                // 獲取錯誤代碼
>                const code = await getCode();
>                /**  
>                 * 控制 useEffect裡的 handleGetReaderList 只做一次初始化 
>                 * 不受之後的呼叫影響狀態，導致 infinite loops     
>                 */
>                if (!readerDropDownRef.current) {
>                    /**
>                     * code為 0: 執行成功、3005: 卡片不存在、5070: 使用者取消操作，可繼續流程;
>                     * code為 61201、61202、61203、61204，以上代碼皆為版本異常情況，可繼續流程;
>                     * 
>                     * TODO 判斷不可使用之版本，若是，需停止流程，此外只需跳燈箱建議更新即可。
>                     * 目前只做版本異常跳燈箱提醒，可繼續流程，不可使用之版本尚未定義。
>                     */  
>                    if (code === 0 || code === 3005 || code === 5070
>                        || code === 61201 || code === 61202 || code === 61203 || code === 61204) {
>                        // 若是安控元件版本異常，跳燈箱(讀卡機安控元件己有最新版本，建議您進行軟體更新。)
>                        if (code === 61201 || code === 61202 || code === 61203 || code === 61204) {
>                            handleOpenErrorModal('ERROR.MESSAGE', code);
>                        }
>                        // 取得讀卡機名稱
>                        handleGetReaderList();
>                    } else {
>                        handleOpenErrorModal('ERROR.MESSAGE', code);
>                    }
>                    readerDropDownRef.current = true;
>                }
>            }
>        };
>        fetchCode();
>    }, [getCode, handleGetReaderList, handleOpenErrorModal, isSCSBWebATMInitialized]);
>```

<br/>

>### [抓取讀卡機資料清單 ── Process 2](#晶片金融卡功能流程-chip-financial-card-function-process)
>```javaScript
>    // 抓取讀卡機資料清單
>    const handleGetReaderList = useCallback(() => {
>        console.debug('handleGetReaderList');
>        const { code, data } = getReaderList();
>        console.debug('handleGetReaderList code', code);
>        console.debug('handleGetReaderList data', data);
>
>        if (code !== 0) {
>            // 不可預期的錯誤。
>            handleOpenErrorModal('COMPONENT.MODAL.TITLE.REMIND', code);
>        }
>
>        if (data.length < 1 || data[0] === '') {
>            dispatch({
>                type: 'OPEN_MODAL',
>               payload: {
>                    title: translate('READER.CARD_READER_NOT_INSTALLED'), // 讀卡機尚未安裝
>                    children: translate('READER_ERRORCODE.SCSB.PLEASE_INSTALL_CARD_READER'), // 請安裝讀卡機! 安裝完讀卡機後，請按下確定。
>                    showCloseBtn: false,
>                    upperRightCloseBtn: false,
>                    backdropStatic: true,
>                    customContent:
>                        <Fragment>
>                            <div className='col-md-auto'>
>                                <Button
>                                    text={translate('COMPONENT.BUTTON.CANCEL')}
>                                    onClick={() => handleClick('CANCEL')}
>                                />
>                            </div>
>                            <div className='col-md-auto'>
>                                <GradientButton
>                                    onClick={handleGetReaderList}
>                                    text={translate('COMPONENT.BUTTON.CONFIRM')}
>                                />
>                            </div>
>                        </Fragment>
>                }
>            });
>        } else {
>            dispatch(
>                {
>                    type: 'SET_READERS',
>                    payload: {
>                        readers: data,
>                        input: { disabled: false },
>                        modal: initialState.modal
>                   }
>                }
>            );
>        }
>    }, [getReaderList, handleOpenErrorModal, translate, handleClick, initialState.modal]);
>```

<br/>

>### [選擇讀卡機 ── Process 3](#晶片金融卡功能流程-chip-financial-card-function-process)
>```javaScript
>    // 選擇讀卡機
>    const handleCardReaderChange = (event) => {
>        const selectedReaderIndex = event.target.value;
>        const selectedReader = state.readers[selectedReaderIndex];
>        inputHandler('reader', selectedReader); // 更新當前選擇的讀卡機
>
>        // 取得卡片狀態
>        getCardStatus(selectedReader).then(({ code }) => {
>            // code為 0: 執行成功、3005: 卡片不存在、5907: 元件認證失敗(在這步可能會有此錯誤)，可繼續流程 
>            if (!(code === 0 || code === 3005 || code === 5907)) {
>                handleOpenErrorModal('COMPONENT.MODAL.TITLE.REMIND', code);
>            } else {
>                console.debug('getCardStatus');
>                console.debug('readerName', selectedReader);
>                console.debug('getCardStatus code', code);
>            }
>        });
>    };
>```

<br/>

>### [取得卡片資訊並驗證的函數 ── Process 4](#晶片金融卡功能流程-chip-financial-card-function-process)
>```javaScript
>    // 取得卡片資訊並驗證的函數
>    const handleSubmit = useCallback((readerName, pinCode) => {
>        const rawData = formState.inputs;
>        const onApiFailure = (msg, rsCode) => {
>            // E9014  為不正確的交易流程 或 'E0025' 系統異常，導到結果頁
>            if (rsCode === 'E9014' || rsCode === 'E0025') {
>                navigate(navigatePath.page4, {
>                    state: {
>                        status: rawData.status.value,
>                        modifyType: rawData.modifyType.value,
>                        errorMsg: msg
>                    },
>                });
>            } else {
>                // 彈跳燈箱顯示錯誤
>                dispatch({
>                    type: 'OPEN_MODAL',
>                    payload: {
>                        title: translate('COMPONENT.MODAL.TITLE.REMIND'),
>                        children: msg,
>                        showCloseBtn: false,
>                        customContent:
>                            <Fragment>
>                                <div className='col-md-auto'>
>                                    <GradientButton
>                                        onClick={() => dispatch({ type: 'CLOSE_MODAL' })}
>                                        text={translate('COMPONENT.BUTTON.CONFIRM')}
>                                    />
>                                </div>
>                            </Fragment>
>                    }
>                });
>            }
>            // 圖形驗證-刷新圖像 
>            if (imageCaptchaRef.current) {
>                imageCaptchaRef.current.refreshImage();
>                inputHandler('picVcode', '');
>            }
>            // 非本人卡片-清空密碼欄
>            inputHandler('pwdText', '');
>        };
>        // 取得卡片資訊
>        getSCSBAccount(readerName).then(({ code, data }) => {
>            console.debug('getSCSBAccount');
>            console.debug('readerName', readerName);
>            console.debug('getSCSBAccount code', code);
>            console.debug('getSCSBAccount data', data);
>
>            if (code === 0 && data) {
>                console.debug('verifyBankCode', data[0].substring(0, 3)); // 取得的銀行代碼
>                console.debug('verifyAccount', data[2].substring(2, 17)); // 取得的銀行帳號
>                // 先取得卡片資訊驗證銀行代碼是否為本行'011'
>                if (data[0].substring(0, 3) !== '011') {
>                    // 顯示提醒視窗 (請插入本行晶片金融卡)
>                    handleOpenErrorModal('COMPONENT.MODAL.TITLE.REMIND', 'READER_ERRORCODE.SCSB.USE_SCSB_CARD_FOR_TRANSACTIONS');
>                    // 圖形驗證-刷新圖像 
>                    if (imageCaptchaRef.current) {
>                        imageCaptchaRef.current.refreshImage();
>                        inputHandler('picVcode', '');
>                    }
>                    // 非本行卡片-清空密碼欄
>                    inputHandler('pwdText', '');
>                } else {
>                    // 驗證密碼
>                    verifyPIN(readerName, pinCode).then(({ code }) => {
>                        console.debug('verifyPIN');
>                        console.debug('readerName', readerName);
>                        console.debug('PIN', pinCode);
>                        console.debug('verifyPIN code', code);
>
>                        // 驗證密碼成功後，在把卡片帳號帶到後端打電文BS1801驗證身分，API成功後導頁
>                        if (code === 0) {
>                            const onApiSuccess = () => {
>                                navigate(navigatePath.page5, { state: { formData: formState.inputs } });
>                            };
>                            // api:新增資料
>                            const doAddCurried = api.doAdd(onApiSuccess, onApiFailure, runPageLoading, stopPageLoading);
>                            doAddCurried(rawData, data[2].substring(2, 17));
>                        } else {
>                            handleOpenErrorModal('COMPONENT.MODAL.TITLE.REMIND', code);
>                            // 卡片不存在 或 使用者取消操作-清空密碼欄
>                            if (code === 3005 || code === 5070) {
>                                inputHandler('pwdText', '');
>                            }
>                            // 圖形驗證-刷新圖像 
>                            if (imageCaptchaRef.current) {
>                                imageCaptchaRef.current.refreshImage();
>                                inputHandler('picVcode', '');
>                            }
>                        }
>                    });
>                }
>            } else {
>                handleOpenErrorModal('COMPONENT.MODAL.TITLE.REMIND', code);
>                // 卡片不存在-清空密碼欄
>                if (code === 3005) {
>                    inputHandler('pwdText', '');
>                }
>                // 圖形驗證-刷新圖像 
>                if (imageCaptchaRef.current) {
>                    imageCaptchaRef.current.refreshImage();
>                    inputHandler('picVcode', '');
>                }
>            }
>        });
>    }, [formState.inputs, getSCSBAccount, handleOpenErrorModal, inputHandler, navigate, navigatePath.page4, navigatePath.page5, runPageLoading, stopPageLoading, translate, verifyPIN]);
>```

<br/>

>### [執行流程中，要導轉至下一頁的前端檢核動作 ── Process 5](#晶片金融卡功能流程-chip-financial-card-function-process)
>```javaScript
> // 導轉至結果頁
>case 'SENDCONFIRM': {
>                // 確保按下確認送出後 元件有正常啟動
>                handleInitSCSBWebATMAdapter();
>
>                // 確保按下確認送出後 是否有讀卡機連接
>                const { code, data } = getReaderList();
>                if (code !== 0 || data.length < 1 || data[0] === '') {
>                    handleGetReaderList();
>                    // 圖形驗證-刷新圖像 
>                    if (imageCaptchaRef.current) {
>                        imageCaptchaRef.current.refreshImage();
>                        inputHandler('picVcode', '');
>                    }
>                    // 有檢核錯誤-清空密碼欄
>                    inputHandler('pwdText', '');
>                } else {
>                    const {
>                        reader: { value: readerVal },
>                        pwdText: { value: pwdTextVal },
>                        picVcode: { value: picVcodeVal }
>                    } = formState.inputs;
>
>                    // 驗證訊息函數
>                    const verificationMsg = (condition, i18nKey) => condition ? translate(i18nKey) : '';
>
>                    // 驗證輸入的字串是否為整數
>                    const validateInput = (inputString) => /^\d+$/.test(inputString);
>
>                    // 驗證清單
>                    const checkList = [
>                        [!isEmpty(pwdTextVal) && (!validateInput(pwdTextVal) || pwdTextVal.length < 6 || pwdTextVal.length > 12), 'READER_ERRORCODE.SCSB.PLEASE_ENTER_6-12_DIGIT_CHIP_PIN'], // 晶片卡密碼請輸入6~12位數字!!
>                        [!isEmpty(picVcodeVal) && (!validateInput(picVcodeVal) || picVcodeVal.length !== 5), 'SE.03.VERIFICATION.MSG_8'], // 圖形驗證碼請輸入5位數字。
>                    ].map(check => verificationMsg(...check)).filter(Boolean);
>
>                    if (checkList.length > 0 || !validate(validationList)) {
>                        if (!validate(validationList)) {
>                            //檢核不通過的話
>                            console.error('檢核不通過');
>                        }
>                        if (checkList.length > 0) {
>                            // 檢核項目列表
>                            const checkElements = checkList.map((item, index) => (
>                                <Fragment key={index}>
>                                    {item}
>                                    {index < checkList.length - 1 && <br />}
>                                </Fragment>
>                            ));
>                            // 顯示提醒視窗
>                            dispatch({
>                                type: 'OPEN_MODAL',
>                                payload: {
>                                    title: translate('COMPONENT.MODAL.TITLE.REMIND'), // 提醒您
>                                    children: checkElements,
>                                    showCloseBtn: true
>                                }
>                            });
>
>                            // 圖形驗證-刷新圖像 
>                            if (imageCaptchaRef.current) {
>                                imageCaptchaRef.current.refreshImage();
>                                inputHandler('picVcode', '');
>                            }
>                            // 有檢核錯誤-清空密碼欄
>                            inputHandler('pwdText', '');
>                        }
>                    } else {
>                        handleSubmit(readerVal, pwdTextVal);
>                    }
>                }
>                break;
>```

<br/>

>### [執行驗證，要導轉至下一頁的前置動作 ── Process 6](#晶片金融卡功能流程-chip-financial-card-function-process)
>```javaScript
>    // 取得卡片資訊並驗證的函數
>    const handleSubmit = useCallback((readerName, pinCode) => {
>        const rawData = formState.inputs;
>        const onApiFailure = (msg, rsCode) => {
>            // E9014  為不正確的交易流程 或 'E0025' 系統異常，導到結果頁
>            if (rsCode === 'E9014' || rsCode === 'E0025') {
>                navigate(navigatePath.page4, {
>                    state: {
>                        status: rawData.status.value,
>                        modifyType: rawData.modifyType.value,
>                        errorMsg: msg
>                    },
>                });
>            } else {
>                // 彈跳燈箱顯示錯誤
>                dispatch({
>                    type: 'OPEN_MODAL',
>                    payload: {
>                        title: translate('COMPONENT.MODAL.TITLE.REMIND'),
>                        children: msg,
>                        showCloseBtn: false,
>                        customContent:
>                            <Fragment>
>                                <div className='col-md-auto'>
>                                    <GradientButton
>                                        onClick={() => dispatch({ type: 'CLOSE_MODAL' })}
>                                        text={translate('COMPONENT.BUTTON.CONFIRM')}
>                                    />
>                                </div>
>                            </Fragment>
>                    }
>                });
>            }
>            // 圖形驗證-刷新圖像 
>            if (imageCaptchaRef.current) {
>                imageCaptchaRef.current.refreshImage();
>                inputHandler('picVcode', '');
>            }
>            // 非本人卡片-清空密碼欄
>            inputHandler('pwdText', '');
>        };
>        // 取得卡片資訊
>        getSCSBAccount(readerName).then(({ code, data }) => {
>            console.debug('getSCSBAccount');
>            console.debug('readerName', readerName);
>            console.debug('getSCSBAccount code', code);
>            console.debug('getSCSBAccount data', data);
>
>            if (code === 0 && data) {
>                console.debug('verifyBankCode', data[0].substring(0, 3)); // 取得的銀行代碼
>                console.debug('verifyAccount', data[2].substring(2, 17)); // 取得的銀行帳號
>                // 先取得卡片資訊驗證銀行代碼是否為本行'011'
>                if (data[0].substring(0, 3) !== '011') {
>                    // 顯示提醒視窗 (請插入本行晶片金融卡)
>                    handleOpenErrorModal('COMPONENT.MODAL.TITLE.REMIND', 'READER_ERRORCODE.SCSB.USE_SCSB_CARD_FOR_TRANSACTIONS');
>                    // 圖形驗證-刷新圖像 
>                    if (imageCaptchaRef.current) {
>                        imageCaptchaRef.current.refreshImage();
>                        inputHandler('picVcode', '');
>                    }
>                    // 非本行卡片-清空密碼欄
>                    inputHandler('pwdText', '');
>                } else {
>                    // 驗證密碼
>                    verifyPIN(readerName, pinCode).then(({ code }) => {
>                        console.debug('verifyPIN');
>                        console.debug('readerName', readerName);
>                        console.debug('PIN', pinCode);
>                        console.debug('verifyPIN code', code);
>
>                        // 驗證密碼成功後，在把卡片帳號帶到後端打電文BS1801驗證身分，API成功後導頁
>                        if (code === 0) {
>                            const onApiSuccess = () => {
>                                navigate(navigatePath.page5, { state: { formData: formState.inputs } });
>                            };
>                            // api:新增資料
>                            const doAddCurried = api.doAdd(onApiSuccess, onApiFailure, runPageLoading, stopPageLoading);
>                            doAddCurried(rawData, data[2].substring(2, 17));
>                        } else {
>                            handleOpenErrorModal('COMPONENT.MODAL.TITLE.REMIND', code);
>                            // 卡片不存在 或 使用者取消操作-清空密碼欄
>                            if (code === 3005 || code === 5070) {
>                                inputHandler('pwdText', '');
>                            }
>                            // 圖形驗證-刷新圖像 
>                            if (imageCaptchaRef.current) {
>                                imageCaptchaRef.current.refreshImage();
>                                inputHandler('picVcode', '');
>                            }
>                        }
>                    });
>                }
>            } else {
>                handleOpenErrorModal('COMPONENT.MODAL.TITLE.REMIND', code);
>                // 卡片不存在-清空密碼欄
>                if (code === 3005) {
>                    inputHandler('pwdText', '');
>                }
>                // 圖形驗證-刷新圖像 
>                if (imageCaptchaRef.current) {
>                    imageCaptchaRef.current.refreshImage();
>                    inputHandler('picVcode', '');
>                }
>            }
>        });
>    }, [formState.inputs, getSCSBAccount, handleOpenErrorModal, inputHandler, navigate, navigatePath.page4, navigatePath.page5, runPageLoading, stopPageLoading, translate, verifyPIN]);
>```

### [使用者操作手冊 ── User Manual](#晶片金融卡功能流程-chip-financial-card-function-process)
>[晶片金融卡元件流程-操作手冊](https://hackmd.io/@eAr5az9oStm0ef3nWj9Qvw/rJeVbPzah)
