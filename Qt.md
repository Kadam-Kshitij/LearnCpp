```cpp
class Base : public QObject {
    Q_OBJECT
signals:
    void signalBase1();
public:
    void base()
    {
        std::cout << "Base1 : " << std::this_thread::get_id() << std::endl;
        emit signalBase1();
    }
};

class Base2 : public QObject {
    Q_OBJECT
public slots:
    void slotBase2()
    {
        sleep(1);
        std::cout << "Base2 : " << std::this_thread::get_id() << std::endl;
    }
    void base2()
    {
    }
};

int main( int argc, char** argv )
{
    QCoreApplication app( argc, argv );
    QThread* th1 = new QThread();
    QThread* th2 = new QThread();

    Base* obj1 = new Base();
    Base2* obj2 = new Base2();
    QObject::connect( obj1, &Base::signalBase1, obj2, &Base2::slotBase2, Qt::DirectConnection );

    obj1->moveToThread( th1 );
    obj2->moveToThread( th2 );
    std::cout << "Main : " << std::this_thread::get_id() << std::endl;
    th1->start();
    th2->start();

    obj1->base();
    obj2->base2();
    app.processEvents();

    app.exec();
}

// With Direct connection
// A direct connection means that the slot is called immediately when the signal
// is emitted, and it is executed in the same thread that emitted the signal. Faster.
// Used when signal and slots are in same threads.
//Main : 139776383104832
//Base1 : 139776383104832
//Base2 : 139776383104832
//Base1 end

// With Queued Connection
// A queued connection means that the slot is called asynchronously when the event 
//loop is running in the receiver's thread. The signal will be placed in the event 
//queue of the receiver's thread, and the slot will be executed later when the event
//loop processes that event. Slower. Used when signal/slot are in different threads.
//Main : 140250582894400
//Base1 : 140250582894400
//Base1 end
//Base2 : 140250498602752

// BlockingQueuedConnection - Same as Queued but sender thread is blocked until the slot is executed.
//Main : 140367347578688
//Base1 : 140367347578688
//Base2 : 140367263287040
//Base1 end

// UniqueConnection
// Ensures that a signal is only connected to a particular slot once, even if the same signal is
// connected multiple times. If the signal is already connected to the slot, the connection is ignored.
// QObject::connect( obj1, &Base::signalBase1, obj2, &Base2::slotBase2, Qt::DirectConnection );
// QObject::connect( obj1, &Base::signalBase1, obj2, &Base2::slotBase2, Qt::UniqueConnection );
```

Event loop checks for events and queues them in event queue and connects them to appropriate handlers.<br>
`return a.exec();  // Starts the event loop`

The Q_OBJECT macro tells the Meta-Object Compiler (moc) to process the class and generate additional code needed for signal-slot connections,<br>
type information, and other features. The moc tool is part of the Qt build process and generates a corresponding .moc file,<br> 
which contains the necessary information for these features.<br>
