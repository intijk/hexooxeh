---
title: shmem_quiet 的保序操作
tags:
  - openshmem
  - shmem
id: 1200
categories:
  - OpenSHMEM
date: 2013-05-17 20:28:22
---

<!--:zh-->shmem quiet 保序操作

    PROGRAM COMPFLAG
      INCLUDE "mpp/shmem.fh"

      INTEGER FLAG_VAR, ARRAY(100), RECEIVER, SENDER
      COMMON/FLAG/FLAG_VAR
      COMMON/DATA/ARRAY
      INTRINSIC MY_PE

      FLAG_VAR = 0
      CALL SHMEM_BARRIER_ALL ! wait for FLAG_VAR to be initialized
      SENDER = 0                        ! PE 0 sends the data
      RECEIVER = 1                      ! PE 1 receives the data

      IF (MY_PE() .EQ. 0) THEN
        ARRAY = 33
        CALL SHMEM_PUT(ARRAY, ARRAY, 100, RECEIVER) ! start sending data
        CALL SHMEM_QUIET                ! wait for delivery
        CALL SHMEM_PUT(FLAG_VAR, 1, 1, RECEIVER) ! send completion flag
      ELSE IF (MY_PE() .EQ. RECEIVER) THEN
        CALL SHMEM_UDCFLUSH
        CALL SHMEM_WAIT(FLAG_VAR, 0)
        PRINT *,ARRAY                       ! ARRAY has been delivered
      ENDIF
    END
    `</pre>
    A调用了一个put，A再调用一个quiet，A再调用一个put，就能保证后面的put是发生在前一个put之后的，上面这个例子有一个状态标记FLAG_VAR,用来标记操作的完成，这个就需要用到保序操作。<!--:--><!--:en-->shmem quiet keep operation sequence
    <pre>`PROGRAM COMPFLAG
      INCLUDE "mpp/shmem.fh"

      INTEGER FLAG_VAR, ARRAY(100), RECEIVER, SENDER
      COMMON/FLAG/FLAG_VAR
      COMMON/DATA/ARRAY
      INTRINSIC MY_PE

      FLAG_VAR = 0
      CALL SHMEM_BARRIER_ALL ! wait for FLAG_VAR to be initialized
      SENDER = 0                        ! PE 0 sends the data
      RECEIVER = 1                      ! PE 1 receives the data

      IF (MY_PE() .EQ. 0) THEN
        ARRAY = 33
        CALL SHMEM_PUT(ARRAY, ARRAY, 100, RECEIVER) ! start sending data
        CALL SHMEM_QUIET                ! wait for delivery
        CALL SHMEM_PUT(FLAG_VAR, 1, 1, RECEIVER) ! send completion flag
      ELSE IF (MY_PE() .EQ. RECEIVER) THEN
        CALL SHMEM_UDCFLUSH
        CALL SHMEM_WAIT(FLAG_VAR, 0)
        PRINT *,ARRAY                       ! ARRAY has been delivered
      ENDIF
    END

A call put(),  then call quiet(), then put() again. If no quiet(), the later put() may reach before  the first one(). When split by quiet(), the first put() can be guaranteed reach before the second put().

About example shows a necessary use of quiet(), there is a status sign FLAG_VAR, to mark finish status of variable operation, it FLAG_VAR should only be marked after the first put operation finished.<!--:-->