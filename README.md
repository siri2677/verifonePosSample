struct ContentView: View {
    // 3. 각 UI 리스너를 대신하는 @State 프로퍼티들
    @State private var selectedPayChip: PayAction = .checkStore
    @State private var deviceIP    = "192.168.50.203"
    @State private var devicePort  = "15000"
    
    @State private var serviceType : ServiceType = .van
    @State private var totalAmount = ""
    @State private var tax         = ""
    @State private var dutyFree    = ""
    @State private var tip         = ""
    @State private var installment = ""
    @State private var taxSetting  : TaxSetting  = .n
    @State private var tradeOpt    : TradeOpt    = .calcDevice
    @State private var taxRate     = ""
    @State private var rateType    : RateType    = .round
    @State private var privacy     = ""
    @State private var cashType    : CashType    = .personal
    @State private var tradeDate   = ""
    @State private var approvalNo  = ""
    @State private var transCode   = ""
    @State private var transNo     = ""
    
    var body: some View {
        VStack(spacing: 0) {
            // AppBar
            Text("KCP X990 TEST (포스연동)")
                .frame(maxWidth: .infinity)
                .padding(.vertical, 20)
                .padding(.leading, 16)
                .background(Color("mainBlue"))
                .foregroundColor(.white)
                .font(.system(size:16, weight: .bold))
            
            ScrollView {
                VStack(alignment: .leading, spacing: 16) {
                    // * 결제요청 구분
                    Text("* 결제요청 구분")
                        .font(.system(size:14, weight:.semibold))
                        .padding(.horizontal, 12)
                    
                    // 4. 가로 스피너: ChipGroup 대체
                    ScrollView(.horizontal, showsIndicators: false) {
                        HStack(spacing:16) {
                            ForEach(PayAction.allCases, id:\.self) { action in
                                Chip(title: action.title, isSelected: selectedPayChip == action)
                                    .onTapGesture { selectedPayChip = action }
                            }
                        }
                        .padding(.horizontal, 10)
                        .padding(.vertical, 8)
                    }
                    
                    // * 요청정보 입력
                    Text("* 요청정보 입력")
                        .font(.system(size:14, weight:.semibold))
                        .padding(.horizontal, 16)
                    
                    // 입력 폼
                    Group {
                        HStack {
                            Label("단말기", systemImage: "desktopcomputer")
                            TextField("IP 입력", text: $deviceIP)
                                .textFieldStyle(RoundedBorderTextFieldStyle())
                            Text(":")
                            TextField("Port", text: $devicePort)
                                .textFieldStyle(RoundedBorderTextFieldStyle())
                                .frame(width: 80)
                        }
                        
                        // 승인방식 (RadioGroup)
                        VStack(alignment: .leading) {
                            Text("승인방식")
                            Picker("", selection: $serviceType) {
                                ForEach(ServiceType.allCases, id:\.self) {
                                    Text($0.rawValue).tag($0)
                                }
                            }
                            .pickerStyle(SegmentedPickerStyle())
                        }
                        
                        Group {
                            TextField("총금액", text: $totalAmount)
                                .keyboardType(.decimalPad)
                                .textFieldStyle(RoundedBorderTextFieldStyle())
                            TextField("세금", text: $tax)
                                .keyboardType(.decimalPad)
                                .textFieldStyle(RoundedBorderTextFieldStyle())
                            TextField("면세", text: $dutyFree)
                                .keyboardType(.decimalPad)
                                .textFieldStyle(RoundedBorderTextFieldStyle())
                            TextField("봉사료", text: $tip)
                                .keyboardType(.decimalPad)
                                .textFieldStyle(RoundedBorderTextFieldStyle())
                            TextField("할부개월", text: $installment)
                                .keyboardType(.numberPad)
                                .textFieldStyle(RoundedBorderTextFieldStyle())
                        }
                        
                        // 복합과세 (RadioGroup)
                        VStack(alignment: .leading) {
                            Text("복합과세")
                            Picker("", selection: $taxSetting) {
                                ForEach(TaxSetting.allCases, id:\.self) {
                                    Text($0.rawValue).tag($0)
                                }
                            }
                            .pickerStyle(SegmentedPickerStyle())
                        }
                        
                        // 거래금액옵션 (RadioGroup)
                        VStack(alignment: .leading) {
                            Text("거래금액옵션")
                            Picker("", selection: $tradeOpt) {
                                ForEach(TradeOpt.allCases, id:\.self) {
                                    Text($0.rawValue).tag($0)
                                }
                            }
                            .pickerStyle(SegmentedPickerStyle())
                        }
                        
                        // 면세 선택 시
                        if tradeOpt == .calcDevice {
                            VStack(alignment:.leading, spacing:8) {
                                Text("세율")
                                TextField("세율(%)", text: $taxRate)
                                    .keyboardType(.decimalPad)
                                    .textFieldStyle(RoundedBorderTextFieldStyle())
                                Text("세금계산방식")
                                Picker("", selection: $rateType) {
                                    ForEach(RateType.allCases, id:\.self) {
                                        Text($0.rawValue).tag($0)
                                    }
                                }
                                .pickerStyle(SegmentedPickerStyle())
                            }
                        }
                        
                        // 현금영수증 등 들어가는 그룹
                        VStack(alignment:.leading, spacing:8) {
                            Text("개인정보")
                            TextField("개인정보 입력", text: $privacy)
                                .textFieldStyle(RoundedBorderTextFieldStyle())
                            Text("발급용도")
                            Picker("", selection: $cashType) {
                                ForEach(CashType.allCases, id:\.self) {
                                    Text($0.rawValue).tag($0)
                                }
                            }
                            .pickerStyle(SegmentedPickerStyle())
                        }
                        
                        // 거래번호 취소 등
                        VStack(alignment:.leading, spacing:8) {
                            Text("승인일자 (YYMMDD)")
                            TextField("YYMMDD", text: $tradeDate)
                                .keyboardType(.numberPad)
                                .textFieldStyle(RoundedBorderTextFieldStyle())
                            Text("승인번호")
                            TextField("승인번호 입력", text: $approvalNo)
                                .textFieldStyle(RoundedBorderTextFieldStyle())
                            Text("거래코드")
                            TextField("코드 입력", text: $transCode)
                                .textFieldStyle(RoundedBorderTextFieldStyle())
                            Text("거래번호")
                            TextField("번호 입력", text: $transNo)
                                .textFieldStyle(RoundedBorderTextFieldStyle())
                        }
                    }
                    .padding(.horizontal, 16)
                }
            }
            
            // 2. 버튼 리스너
            Button(action: {
                // 요청하기 액션
                print("요청하기 tapped with payMethod:", selectedPayChip)
            }) {
                Text("요청하기")
                    .foregroundColor(.white)
                    .frame(maxWidth: .infinity)
                    .padding(.vertical, 16)
                    .background(Color("mainBlue"))
            }
        }
        .edgesIgnoringSafeArea(.top)
    }
}

// 1. data class → enum 으로 대체
enum PayAction: CaseIterable {
    case checkStore, creditAuth, creditCancel, cashICAuth, cashICCancel
    case unionAuth, unionCancel, cashReceiptAuth, cashReceiptCancel
    case easyPayAuth, easyPayCancel, transNoCancel, print, barcodeReq
    
    var title: String {
        switch self {
        case .checkStore:      return "가맹점 정보 확인"
        case .creditAuth:      return "카드 승인"
        case .creditCancel:    return "카드 취소"
        case .cashICAuth:      return "현금IC 승인"
        case .cashICCancel:    return "현금IC 취소"
        case .unionAuth:       return "은련 승인"
        case .unionCancel:     return "은련 취소"
        case .cashReceiptAuth: return "현금영수증 승인"
        case .cashReceiptCancel: return "현금영수증 취소"
        case .easyPayAuth:     return "간편결제 승인"
        case .easyPayCancel:   return "간편결제 취소"
        case .transNoCancel:   return "거래번호 취소"
        case .print:           return "프린트 출력"
        case .barcodeReq:      return "바코드 요청"
        }
    }
}

// Radio 그룹용 enum
enum ServiceType: String, CaseIterable { case van="VAN", pg="PG" }
enum TaxSetting: String, CaseIterable { case y="Y", n="N" }
enum TradeOpt: String, CaseIterable { case calcPos="포스계산", calcDevice="단말기계산" }
enum RateType: String, CaseIterable { case round="반올림", ceil="올림", floor="버림" }
enum CashType: String, CaseIterable { case personal="소득공제", busi="지출증빙", selfIssue="자진발급" }

// 4. 커스텀 Chip 뷰
struct Chip: View {
    let title: String
    let isSelected: Bool
    var body: some View {
        Text(title)
            .padding(.horizontal,12)
            .padding(.vertical,8)
            .background(isSelected ? Color("mainBlue") : Color.gray.opacity(0.2))
            .foregroundColor(isSelected ? .white : .primary)
            .cornerRadius(16)
    }
}
