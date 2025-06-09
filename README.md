import CocoaAsyncSocket  // GCDAsyncSocket

class SocketClient: NSObject, GCDAsyncSocketDelegate {
    private var socket: GCDAsyncSocket!

    override init() {
        super.init()
        socket = GCDAsyncSocket(delegate: self, delegateQueue: .main)
    }

    func connect(host: String, port: UInt16) {
        try? socket.connect(toHost: host, onPort: port)
    }

    // 서버 연결 성공
    func socket(_ sock: GCDAsyncSocket, didConnectToHost host: String, port: UInt16) {
        print("연결 성공:", host, port)
        // 메시지 수신 대기
        sock.readData(withTimeout: -1, tag: 0)
    }

    // 데이터 수신
    func socket(_ sock: GCDAsyncSocket, didRead data: Data, withTag tag: Int) {
        if let str = String(data: data, encoding: .utf8) {
            print("받은 메시지:", str)
        }
        // 계속 읽기
        sock.readData(withTimeout: -1, tag: 0)
    }

    // 메시지 전송
    func send(_ message: String) {
        let data = message.data(using: .utf8)!
        socket.write(data, withTimeout: -1, tag: 0)
    }
}
