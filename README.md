WatchedRotatingFileHandler
==========================

WatchedRotatingFileHandler is a file handler which is safe for multiple processes rotating log file(tested on Linux). 

It a combination of RotatingFileHandler and WatchedFileHandler, adapted from python's logging handlers, enhanced with file lock to prevent processes contend to rotate the log file.

Example
-------

    from watched_rotating_log import WatchedRotatingFileHandler
    import multiprocessing

    logger = logging.getLogger('test')
    logger.setLevel(logging.DEBUG)
    logfile = os.path.join('/tmp/test.log')
    hdlr = WatchedRotatingFileHandler(logfile, 'a', maxBytes=1024*1024, backupCount=5)
    hdlr.setFormatter(logging.Formatter('%(asctime)s %(process)s: %(message)s'))
    logger.addHandler(hdlr)

    def do():
        while True:
            logger.info('test')
            time.sleep(0.001)

    processes = []
    for i in range(4):
        process = multiprocessing.Process(target=do)
        processes.append(process)
        process.start()

    for process in processes:
        process.join()

