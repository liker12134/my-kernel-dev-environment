

#### [RFC PATCH v6 00/22] virtio/vsock: introduce SOCK_SEQPACKET support
##### From: Arseny Krasnov <arseny.krasnov@kaspersky.com>

```c
From patchwork Sun Mar  7 17:57:19 2021
Content-Type: text/plain; charset="utf-8"
MIME-Version: 1.0
Content-Transfer-Encoding: 7bit
X-Patchwork-Submitter: Arseny Krasnov <arseny.krasnov@kaspersky.com>
X-Patchwork-Id: 12120895
Return-Path: <SRS0=+yTQ=IF=vger.kernel.org=kvm-owner@kernel.org>
X-Spam-Checker-Version: SpamAssassin 3.4.0 (2014-02-07) on
	aws-us-west-2-korg-lkml-1.web.codeaurora.org
X-Spam-Level: 
X-Spam-Status: No, score=-19.0 required=3.0 tests=BAYES_00,DKIMWL_WL_HIGH,
	DKIM_SIGNED,DKIM_VALID,DKIM_VALID_AU,HEADER_FROM_DIFFERENT_DOMAINS,
	INCLUDES_CR_TRAILER,INCLUDES_PATCH,MAILING_LIST_MULTI,SPF_HELO_NONE,SPF_PASS,
	USER_AGENT_GIT autolearn=unavailable autolearn_force=no version=3.4.0
Received: from mail.kernel.org (mail.kernel.org [198.145.29.99])
	by smtp.lore.kernel.org (Postfix) with ESMTP id DD9D2C433E6
	for <kvm@archiver.kernel.org>; Sun,  7 Mar 2021 17:59:06 +0000 (UTC)
Received: from vger.kernel.org (vger.kernel.org [23.128.96.18])
	by mail.kernel.org (Postfix) with ESMTP id B0A626518D
	for <kvm@archiver.kernel.org>; Sun,  7 Mar 2021 17:59:06 +0000 (UTC)
Received: (majordomo@vger.kernel.org) by vger.kernel.org via listexpand
        id S231713AbhCGR6d (ORCPT <rfc822;kvm@archiver.kernel.org>);
        Sun, 7 Mar 2021 12:58:33 -0500
Received: from mx13.kaspersky-labs.com ([91.103.66.164]:48915 "EHLO
        mx13.kaspersky-labs.com" rhost-flags-OK-OK-OK-OK) by vger.kernel.org
        with ESMTP id S230503AbhCGR6M (ORCPT <rfc822;kvm@vger.kernel.org>);
        Sun, 7 Mar 2021 12:58:12 -0500
Received: from relay13.kaspersky-labs.com (unknown [127.0.0.10])
        by relay13.kaspersky-labs.com (Postfix) with ESMTP id A9ECD521036;
        Sun,  7 Mar 2021 20:58:05 +0300 (MSK)
DKIM-Signature: v=1; a=rsa-sha256; c=relaxed/relaxed; d=kaspersky.com;
        s=mail202102; t=1615139885;
        bh=NR6fqczSPE3L//tu/oG3y++CW48ALi4JbNINbRcp+1g=;
        h=From:To:Subject:Date:Message-ID:MIME-Version:Content-Type;
        b=uPHwL1LfASK9PcaJhZ8obezkst9HKp1Zdlbpaks1s5DTOhRObCVGZ33rr8ENwtxOq
         Gptp1Rq3KZQNnnT0UDnj4FBvA9HYryJbgImclT9zNkB7IUJDpIy4rWxx+LtTK96Rxs
         Ok4HWbk2Mj2VbxnN44V87yB/1eOtbL8iaWRA5SB7zrbxXit2+1VFfq4gYKMsA8Ht5C
         ozCmLqplWhRUNz5QXoUZB8YZeA8gDylY5sNeY81OrWUPn1JBVO1a/7fUWKvnruXzfc
         +hrxRPvEOaVfd5mtFPGixIklofQykhQukvQuooD+/qpAh5AajS4+8uMM2otcNn2kiz
         ZbLwUWHjPH6hg==
Received: from mail-hq2.kaspersky.com (unknown [91.103.66.206])
        (using TLSv1.2 with cipher ECDHE-RSA-AES128-SHA256 (128/128 bits))
        (Client CN "mail-hq2.kaspersky.com",
 Issuer "Kaspersky MailRelays CA G3" (verified OK))
        by mailhub13.kaspersky-labs.com (Postfix) with ESMTPS id 1D8FF521275;
        Sun,  7 Mar 2021 20:58:00 +0300 (MSK)
Received: from arseniy-pc.avp.ru (10.64.64.121) by hqmailmbx3.avp.ru
 (10.64.67.243) with Microsoft SMTP Server (version=TLS1_2,
 cipher=TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256) id 15.1.2176.2; Sun, 7 Mar
 2021 20:58:00 +0300
From: Arseny Krasnov <arseny.krasnov@kaspersky.com>
To: Stefan Hajnoczi <stefanha@redhat.com>,
        Stefano Garzarella <sgarzare@redhat.com>,
        "Michael S. Tsirkin" <mst@redhat.com>,
        Jason Wang <jasowang@redhat.com>,
        "David S. Miller" <davem@davemloft.net>,
        Jakub Kicinski <kuba@kernel.org>,
        Arseny Krasnov <arseny.krasnov@kaspersky.com>,
        Jorgen Hansen <jhansen@vmware.com>,
        Norbert Slusarek <nslusarek@gmx.net>,
        Andra Paraschiv <andraprs@amazon.com>,
        Colin Ian King <colin.king@canonical.com>
CC: <kvm@vger.kernel.org>, <virtualization@lists.linux-foundation.org>,
        <netdev@vger.kernel.org>, <linux-kernel@vger.kernel.org>,
        <stsp2@yandex.ru>, <oxffffaa@gmail.com>
Subject: [RFC PATCH v6 00/22] virtio/vsock: introduce SOCK_SEQPACKET support
Date: Sun, 7 Mar 2021 20:57:19 +0300
Message-ID: <20210307175722.3464068-1-arseny.krasnov@kaspersky.com>
X-Mailer: git-send-email 2.25.1
MIME-Version: 1.0
X-Originating-IP: [10.64.64.121]
X-ClientProxiedBy: hqmailmbx2.avp.ru (10.64.67.242) To hqmailmbx3.avp.ru
 (10.64.67.243)
X-KSE-ServerInfo: hqmailmbx3.avp.ru, 9
X-KSE-AntiSpam-Interceptor-Info: scan successful
X-KSE-AntiSpam-Version: 5.9.20, Database issued on: 03/07/2021 17:45:01
X-KSE-AntiSpam-Status: KAS_STATUS_NOT_DETECTED
X-KSE-AntiSpam-Method: none
X-KSE-AntiSpam-Rate: 10
X-KSE-AntiSpam-Info: Lua profiles 162254 [Mar 07 2021]
X-KSE-AntiSpam-Info: LuaCore: 431 431 6af1f0c9661e70e28927a654c0fea10ff13ade05
X-KSE-AntiSpam-Info: Version: 5.9.20.0
X-KSE-AntiSpam-Info: Envelope from: arseny.krasnov@kaspersky.com
X-KSE-AntiSpam-Info: {Prob_from_in_msgid}
X-KSE-AntiSpam-Info: {Tracking_from_domain_doesnt_match_to}
X-KSE-AntiSpam-Info: {Macro_CONTENT_TYPE_ENCODING_NOT_JAPANESE}
X-KSE-AntiSpam-Info: {Macro_CONTENT_TYPE_ENCODING_NOT_RUS}
X-KSE-AntiSpam-Info: {Macro_CONTENT_TYPE_MISSED}
X-KSE-AntiSpam-Info: {Macro_DATE_DOUBLE_SPACE}
X-KSE-AntiSpam-Info: {Macro_DATE_MOSCOW}
X-KSE-AntiSpam-Info: {Macro_FROM_DOUBLE_ENG_NAME}
X-KSE-AntiSpam-Info: {Macro_FROM_LOWCAPS_DOUBLE_ENG_NAME_IN_EMAIL}
X-KSE-AntiSpam-Info: {Macro_FROM_NOT_RU}
X-KSE-AntiSpam-Info: {Macro_FROM_NOT_RUS_CHARSET}
X-KSE-AntiSpam-Info: {Macro_FROM_REAL_NAME_MATCHES_ALL_USERNAME_PROB}
X-KSE-AntiSpam-Info: {Macro_HEADERS_NOT_LIST}
X-KSE-AntiSpam-Info: {Macro_MAILER_OTHER}
X-KSE-AntiSpam-Info: {Macro_MISC_X_PRIORITY_MISSED}
X-KSE-AntiSpam-Info: {Macro_NO_DKIM}
X-KSE-AntiSpam-Info: {Macro_REPLY_TO_MISSED}
X-KSE-AntiSpam-Info: {Macro_SUBJECT_AT_LEAST_2_WORDS}
X-KSE-AntiSpam-Info: {Macro_SUBJECT_LONG_TEXT}
X-KSE-AntiSpam-Info: {Macro_TO_CONTAINS_5_EMAILS}
X-KSE-AntiSpam-Info: {Macro_TO_CONTAINS_SEVERAL_EMAILS}
X-KSE-AntiSpam-Info: 
 127.0.0.199:7.1.2;arseniy-pc.avp.ru:7.1.1;d41d8cd98f00b204e9800998ecf8427e.com:7.1.1;kaspersky.com:7.1.1
X-KSE-AntiSpam-Info: Rate: 10
X-KSE-AntiSpam-Info: Status: not_detected
X-KSE-AntiSpam-Info: Method: none
X-KSE-Antiphishing-Info: Clean
X-KSE-Antiphishing-ScanningType: Deterministic
X-KSE-Antiphishing-Method: None
X-KSE-Antiphishing-Bases: 03/07/2021 17:47:00
X-KSE-AttachmentFiltering-Interceptor-Info: no applicable attachment filtering
 rules found
X-KSE-Antivirus-Interceptor-Info: scan successful
X-KSE-Antivirus-Info: Clean, bases: 07.03.2021 15:50:00
X-KSE-BulkMessagesFiltering-Scan-Result: InTheLimit
X-KSE-AttachmentFiltering-Interceptor-Info: no applicable attachment filtering
 rules found
X-KSE-BulkMessagesFiltering-Scan-Result: InTheLimit
X-KLMS-Rule-ID: 52
X-KLMS-Message-Action: clean
X-KLMS-AntiSpam-Status: not scanned, disabled by settings
X-KLMS-AntiSpam-Interceptor-Info: not scanned
X-KLMS-AntiPhishing: Clean, bases: 2021/03/07 17:11:00
X-KLMS-AntiVirus: Kaspersky Security for Linux Mail Server, version 8.0.3.30,
 bases: 2021/03/07 15:50:00 #16360637
X-KLMS-AntiVirus-Status: Clean, skipped
Precedence: bulk
List-ID: <kvm.vger.kernel.org>
X-Mailing-List: kvm@vger.kernel.org

This patchset implements support of SOCK_SEQPACKET for virtio
transport.
	As SOCK_SEQPACKET guarantees to save record boundaries, so to
do it, two new packet operations were added: first for start of record
 and second to mark end of record(SEQ_BEGIN and SEQ_END later). Also,
both operations carries metadata - to maintain boundaries and payload
integrity. Metadata is introduced by adding special header with two
fields - message id and message length:

	struct virtio_vsock_seq_hdr {
		__le32  msg_id;
		__le32  msg_len;
	} __attribute__((packed));

	This header is transmitted as payload of SEQ_BEGIN and SEQ_END
packets(buffer of second virtio descriptor in chain) in the same way as
data transmitted in RW packets. Payload was chosen as buffer for this
header to avoid touching first virtio buffer which carries header of
packet, because someone could check that size of this buffer is equal
to size of packet header. To send record, packet with start marker is
sent first(it's header carries length of record and id),then all data
is sent as usual 'RW' packets and finally SEQ_END is sent(it carries
id of message, which is equal to id of SEQ_BEGIN), also after sending
SEQ_END id is incremented. On receiver's side,size of record is known
from packet with start record marker. To check that no packets were
dropped by transport, 'msg_id's of two sequential SEQ_BEGIN and SEQ_END
are checked to be equal and length of data between two markers is
compared to then length in SEQ_BEGIN header.
	Now as  packets of one socket are not reordered neither on
vsock nor on vhost transport layers, such markers allows to restore
original record on receiver's side. If user's buffer is smaller that
record length, when all out of size data is dropped.
	Maximum length of datagram is not limited as in stream socket,
because same credit logic is used. Difference with stream socket is
that user is not woken up until whole record is received or error
occurred. Implementation also supports 'MSG_EOR' and 'MSG_TRUNC' flags.
	Tests also implemented.

	Thanks to stsp2@yandex.ru for encouragements and initial design
recommendations.

 Arseny Krasnov (22):
  af_vsock: update functions for connectible socket
  af_vsock: separate wait data loop
  af_vsock: separate receive data loop
  af_vsock: implement SEQPACKET receive loop
  af_vsock: separate wait space loop
  af_vsock: implement send logic for SEQPACKET
  af_vsock: rest of SEQPACKET support
  af_vsock: update comments for stream sockets
  virtio/vsock: set packet's type in virtio_transport_send_pkt_info()
  virtio/vsock: simplify credit update function API
  virtio/vsock: dequeue callback for SOCK_SEQPACKET
  virtio/vsock: fetch length for SEQPACKET record
  virtio/vsock: add SEQPACKET receive logic
  virtio/vsock: rest of SOCK_SEQPACKET support
  virtio/vsock: SEQPACKET feature bit
  vhost/vsock: SEQPACKET feature bit support
  virtio/vsock: SEQPACKET feature bit support
  virtio/vsock: setup SEQPACKET ops for transport
  vhost/vsock: setup SEQPACKET ops for transport
  vsock/loopback: setup SEQPACKET ops for transport
  vsock_test: add SOCK_SEQPACKET tests
  virtio/vsock: update trace event for SEQPACKET

 drivers/vhost/vsock.c                        |  22 +-
 include/linux/virtio_vsock.h                 |  22 +
 include/net/af_vsock.h                       |  10 +
 .../events/vsock_virtio_transport_common.h   |  48 +-
 include/uapi/linux/virtio_vsock.h            |  19 +
 net/vmw_vsock/af_vsock.c                     | 589 +++++++++++------
 net/vmw_vsock/virtio_transport.c             |  18 +
 net/vmw_vsock/virtio_transport_common.c      | 364 ++++++++--
 net/vmw_vsock/vsock_loopback.c               |  13 +
 tools/testing/vsock/util.c                   |  32 +-
 tools/testing/vsock/util.h                   |   3 +
 tools/testing/vsock/vsock_test.c             | 126 ++++
 12 files changed, 1013 insertions(+), 253 deletions(-)

 v5 -> v6:
 General changelog:
 - virtio transport specific callbacks which send SEQ_BEGIN or
   SEQ_END now hidden inside virtio transport. Only enqueue,
   dequeue and record length callbacks are provided by transport.

 - virtio feature bit for SEQPACKET socket support introduced:
   VIRTIO_VSOCK_F_SEQPACKET.

 - 'msg_cnt' field in 'struct virtio_vsock_seq_hdr' renamed to
   'msg_id' and used as id.

 Per patch changelog:
 - 'af_vsock: separate wait data loop':
    1) Commit message updated.
    2) 'prepare_to_wait()' moved inside while loop(thanks to
      Jorgen Hansen).
    Marked 'Reviewed-by' with 1), but as 2) I removed R-b.

 - 'af_vsock: separate receive data loop': commit message
    updated.
    Marked 'Reviewed-by' with that fix.

 - 'af_vsock: implement SEQPACKET receive loop': style fixes.

 - 'af_vsock: rest of SEQPACKET support':
    1) 'module_put()' added when transport callback check failed.
    2) Now only 'seqpacket_allow()' callback called to check
       support of SEQPACKET by transport.

 - 'af_vsock: update comments for stream sockets': commit message
    updated.
    Marked 'Reviewed-by' with that fix.

 - 'virtio/vsock: set packet's type in send':
    1) Commit message updated.
    2) Parameter 'type' from 'virtio_transport_send_credit_update()'
       also removed in this patch instead of in next.

 - 'virtio/vsock: dequeue callback for SOCK_SEQPACKET': SEQPACKET
    related state wrapped to special struct.

 - 'virtio/vsock: update trace event for SEQPACKET': format strings
    now not broken by new lines.

 v4 -> v5:
 - patches reorganized:
   1) Setting of packet's type in 'virtio_transport_send_pkt_info()'
      is moved to separate patch.
   2) Simplifying of 'virtio_transport_send_credit_update()' is
      moved to separate patch and before main virtio/vsock patches.
 - style problem fixed
 - in 'af_vsock: separate receive data loop' extra 'release_sock()'
   removed
 - added trace event fields for SEQPACKET
 - in 'af_vsock: separate wait data loop':
   1) 'vsock_wait_data()' removed 'goto out;'
   2) Comment for invalid data amount is changed.
 - in 'af_vsock: rest of SEQPACKET support', 'new_transport' pointer
   check is moved after 'try_module_get()'
 - in 'af_vsock: update comments for stream sockets', 'connect-oriented'
   replaced with 'connection-oriented'
 - in 'loopback/vsock: setup SEQPACKET ops for transport',
   'loopback/vsock' replaced with 'vsock/loopback'

 v3 -> v4:
 - SEQPACKET specific metadata moved from packet header to payload
   and called 'virtio_vsock_seq_hdr'
 - record integrity check:
   1) SEQ_END operation was added, which marks end of record.
   2) Both SEQ_BEGIN and SEQ_END carries counter which is incremented
      on every marker send.
 - af_vsock.c: socket operations for STREAM and SEQPACKET call same
   functions instead of having own "gates" differs only by names:
   'vsock_seqpacket/stream_getsockopt()' now replaced with
   'vsock_connectible_getsockopt()'.
 - af_vsock.c: 'seqpacket_dequeue' callback returns error and flag that
   record ready. There is no need to return number of copied bytes,
   because case when record received successfully is checked at virtio
   transport layer, when SEQ_END is processed. Also user doesn't need
   number of copied bytes, because 'recv()' from SEQPACKET could return
   error, length of users's buffer or length of whole record(both are
   known in af_vsock.c).
 - af_vsock.c: both wait loops in af_vsock.c(for data and space) moved
   to separate functions because now both called from several places.
 - af_vsock.c: 'vsock_assign_transport()' checks that 'new_transport'
   pointer is not NULL and returns 'ESOCKTNOSUPPORT' instead of 'ENODEV'
   if failed to use transport.
 - tools/testing/vsock/vsock_test.c: rename tests

 v2 -> v3:
 - patches reorganized: split for prepare and implementation patches
 - local variables are declared in "Reverse Christmas tree" manner
 - virtio_transport_common.c: valid leXX_to_cpu() for vsock header
   fields access
 - af_vsock.c: 'vsock_connectible_*sockopt()' added as shared code
   between stream and seqpacket sockets.
 - af_vsock.c: loops in '__vsock_*_recvmsg()' refactored.
 - af_vsock.c: 'vsock_wait_data()' refactored.

 v1 -> v2:
 - patches reordered: af_vsock.c related changes now before virtio vsock
 - patches reorganized: more small patches, where +/- are not mixed
 - tests for SOCK_SEQPACKET added
 - all commit messages updated
 - af_vsock.c: 'vsock_pre_recv_check()' inlined to
   'vsock_connectible_recvmsg()'
 - af_vsock.c: 'vsock_assign_transport()' returns ENODEV if transport
   was not found
 - virtio_transport_common.c: transport callback for seqpacket dequeue
 - virtio_transport_common.c: simplified
   'virtio_transport_recv_connected()'
 - virtio_transport_common.c: send reset on socket and packet type
			      mismatch.

Signed-off-by: Arseny Krasnov <arseny.krasnov@kaspersky.com>
```
