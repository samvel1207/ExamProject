# ExamProject

Transaction management
==============================
Assume we develop a database supporting transactions. Further assume that there is an existing "Transaction" class you will use to start, commit, or abort an transaction with the following public declaration:

    class Transaction {
    public:
        Transaction(Database& db);
        ~Transaction();
        
        /// Starts the TX. Illegal to call if the TX already started.
        void start();
        
        /// Commits a started TX. Illegal to call if TX was not yet started or is already committed/aborted.
        void commit();

        /// Aborts a started TX. Illegal to call if TX was not yet started or is already committed/aborted.
        void abort();
    }

The following threading rules apply to the Transaction class:

* One thread, one transaction: a thread may have a single transaction in use at any time
* One transaction, one thread: a transaction object may be used only from its creating thread
* Multiple transactions may be executed concurrently (using multiple threads)

Your task is to simplify working with transactions using lambdas: implement a class called "TxRunner" that runs any given code inside a transaction taking care of managing all interactions with the Transaction class. The given code is called with a reference to Database (only; no need to pass a Transaction).

This is the public declaration of TxRunner you are asked to implement:

    /// db is provided by the caller and is guaranteed to be valid for the live time of TxRunner
    TxRunner(Database& db);

    /// Runs the given function inside a transaction, which is committed after the given function returns (normally).
    /// The given function may throw an exception, in which case the transaction is aborted (not committed).
    /// FUNCTION is any lambda accepting a reference to the Database object as parameter.
    template <typename FUNCTION>
    void runTransactional(FUNCTION fun);
    
A TxRunner object must allow recursive calls of runTransactional. In this case the most outer call defines the boundaries of the (only) transaction - there are no "nested" sub transactions etc.

Also, a TxRunner object must be thread safe as it may be called from any number of threads. Allow maximum concurrency according to the Transaction class' threading rules above.

Note, that you do not need to take care of multiple TxRunner instances; just assume only one is allocated globally.