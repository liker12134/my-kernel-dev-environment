

#### [PATCH v2 00/19] virtio-mem: Paravirtualized memory hot(un)plug
##### From: David Hildenbrand <david@redhat.com>

```c
From patchwork Wed May 20 12:31:33 2020
Content-Type: text/plain; charset="utf-8"
MIME-Version: 1.0
Content-Transfer-Encoding: 7bit
X-Patchwork-Submitter: David Hildenbrand <david@redhat.com>
X-Patchwork-Id: 11560117
Return-Path: <SRS0=QMHo=7C=vger.kernel.org=kvm-owner@kernel.org>
Received: from mail.kernel.org (pdx-korg-mail-1.web.codeaurora.org
 [172.30.200.123])
	by pdx-korg-patchwork-2.web.codeaurora.org (Postfix) with ESMTP id 853DC90
	for <patchwork-kvm@patchwork.kernel.org>;
 Wed, 20 May 2020 12:32:18 +0000 (UTC)
Received: from vger.kernel.org (vger.kernel.org [23.128.96.18])
	by mail.kernel.org (Postfix) with ESMTP id 66D8220756
	for <patchwork-kvm@patchwork.kernel.org>;
 Wed, 20 May 2020 12:32:18 +0000 (UTC)
Authentication-Results: mail.kernel.org;
	dkim=pass (1024-bit key) header.d=redhat.com header.i=@redhat.com
 header.b="a04jdOBz"
Received: (majordomo@vger.kernel.org) by vger.kernel.org via listexpand
        id S1726693AbgETMcR (ORCPT
        <rfc822;patchwork-kvm@patchwork.kernel.org>);
        Wed, 20 May 2020 08:32:17 -0400
Received: from us-smtp-1.mimecast.com ([207.211.31.81]:37454 "EHLO
        us-smtp-delivery-1.mimecast.com" rhost-flags-OK-OK-OK-FAIL)
        by vger.kernel.org with ESMTP id S1726439AbgETMcP (ORCPT
        <rfc822;kvm@vger.kernel.org>); Wed, 20 May 2020 08:32:15 -0400
DKIM-Signature: v=1; a=rsa-sha256; c=relaxed/relaxed; d=redhat.com;
        s=mimecast20190719; t=1589977933;
        h=from:from:reply-to:subject:subject:date:date:message-id:message-id:
         to:to:cc:cc:mime-version:mime-version:
         content-transfer-encoding:content-transfer-encoding;
        bh=vL+qjecBZP5AC3zOCUzsS8QgtP22+S85mI62PeHV/qk=;
        b=a04jdOBzMpSzmy2dO6oAbjM9+rE0DoVvi4po1K35o/imEwhaGv+fkEsPhoHIrgb65ysygm
        lnNbRqkJJeqjnL3epNN1BOBVSANI15GMIEQmAY67d0ljx8/vUVM5tgw1uFLofxufkc60dH
        ZyH98iPWCBFX8SYbVkFoYWXhUofoctU=
Received: from mimecast-mx01.redhat.com (mimecast-mx01.redhat.com
 [209.132.183.4]) (Using TLS) by relay.mimecast.com with ESMTP id
 us-mta-285-MZxo64u-MJGFGd615jCGwg-1; Wed, 20 May 2020 08:32:10 -0400
X-MC-Unique: MZxo64u-MJGFGd615jCGwg-1
Received: from smtp.corp.redhat.com (int-mx05.intmail.prod.int.phx2.redhat.com
 [10.5.11.15])
        (using TLSv1.2 with cipher AECDH-AES256-SHA (256/256 bits))
        (No client certificate requested)
        by mimecast-mx01.redhat.com (Postfix) with ESMTPS id 018B08014D7;
        Wed, 20 May 2020 12:32:08 +0000 (UTC)
Received: from t480s.redhat.com (ovpn-113-76.ams2.redhat.com [10.36.113.76])
        by smtp.corp.redhat.com (Postfix) with ESMTP id 726B062B10;
        Wed, 20 May 2020 12:31:53 +0000 (UTC)
From: David Hildenbrand <david@redhat.com>
To: qemu-devel@nongnu.org
Cc: kvm@vger.kernel.org, qemu-s390x@nongnu.org,
        Richard Henderson <rth@twiddle.net>,
        Paolo Bonzini <pbonzini@redhat.com>,
        "Dr . David Alan Gilbert" <dgilbert@redhat.com>,
        Eduardo Habkost <ehabkost@redhat.com>,
        "Michael S . Tsirkin" <mst@redhat.com>,
        David Hildenbrand <david@redhat.com>,
        teawater <teawaterz@linux.alibaba.com>,
        Pankaj Gupta <pankaj.gupta.linux@gmail.com>,
        Alex Williamson <alex.williamson@redhat.com>,
        Christian Borntraeger <borntraeger@de.ibm.com>,
        Cornelia Huck <cohuck@redhat.com>,
        Eric Blake <eblake@redhat.com>,
        Eric Farman <farman@linux.ibm.com>,
        Hailiang Zhang <zhang.zhanghailiang@huawei.com>,
        Halil Pasic <pasic@linux.ibm.com>,
        Igor Mammedov <imammedo@redhat.com>,
        Janosch Frank <frankja@linux.ibm.com>,
        Juan Quintela <quintela@redhat.com>,
        Keith Busch <kbusch@kernel.org>,
        Marcel Apfelbaum <marcel.apfelbaum@gmail.com>,
        Markus Armbruster <armbru@redhat.com>,
        Peter Maydell <peter.maydell@linaro.org>,
        Pierre Morel <pmorel@linux.ibm.com>,
        Tony Krowiak <akrowiak@linux.ibm.com>
Subject: [PATCH v2 00/19] virtio-mem: Paravirtualized memory hot(un)plug
Date: Wed, 20 May 2020 14:31:33 +0200
Message-Id: <20200520123152.60527-1-david@redhat.com>
MIME-Version: 1.0
X-Scanned-By: MIMEDefang 2.79 on 10.5.11.15
Sender: kvm-owner@vger.kernel.org
Precedence: bulk
List-ID: <kvm.vger.kernel.org>
X-Mailing-List: kvm@vger.kernel.org

This is the very basic, initial version of virtio-mem. More info on
virtio-mem in general can be found in the Linux kernel driver posting [1]
and in patch #10.

The patches can be found at:
https://github.com/davidhildenbrand/qemu.git virtio-mem-v2

"The basic idea of virtio-mem is to provide a flexible,
cross-architecture memory hot(un)plug solution that avoids many limitations
imposed by existing technologies, architectures, and interfaces."

There are a lot of addons in the works (esp. protection of unplugged
memory, better hugepage support (esp. when reading unplugged memory),
resizeable memory backends, migration optimizations, support for more
architectures, ...), this is the very basic version to get the ball
rolling.

The first 8 patches make sure we don't have any sudden surprises e.g., if
somebody tries to pin all memory in RAM blocks, resulting in a higher
memory consumption than desired. The remaining patches add basic virtio-mem
along with support for x86-64.

[1] https://lkml.kernel.org/r/20200311171422.10484-1-david@redhat.com

Cc: teawater <teawaterz@linux.alibaba.com>
Cc: Pankaj Gupta <pankaj.gupta.linux@gmail.com>

v1 -> v2:
- Rebased to object_property_*() changes
- "exec: Introduce ram_block_discard_(disable|require)()"
-- Change the function names and rephrase/add comments
- "virtio-balloon: Rip out qemu_balloon_inhibit()"
-- Add and use "migration_in_incoming_postcopy()"
- "migration/rdma: Use ram_block_discard_disable()"
-- Add a comment regarding pin_all vs. !pin_all
- "virtio-mem: Paravirtualized memory hot(un)plug"
-- Replace virtio_mem_discard_inhibited() by
   migration_in_incoming_postcopy()
-- Drop some asserts
-- Drop virtio_mem_bad_request(), use virtio_error() directly, printing
   more information
-- Replace "Note: Discarding should never fail ..." comments by
   error_report()
-- Replace virtio_stw_p() by cpu_to_le16()
-- Drop migration_addr and migration_block_size
-- Minor cleanups
- "linux-headers: update to contain virtio-mem"
-- Updated to latest v4 in Linux
- General changes
-- Fixup the users of the renamed ram_block_discard_(disable|require)
-- Use "X: cannot disable RAM discard"-styled error messages
- Added
-- "virtio-mem: Migration sanity checks"
-- "virtio-mem: Add trace events"

David Hildenbrand (19):
  exec: Introduce ram_block_discard_(disable|require)()
  vfio: Convert to ram_block_discard_disable()
  accel/kvm: Convert to ram_block_discard_disable()
  s390x/pv: Convert to ram_block_discard_disable()
  virtio-balloon: Rip out qemu_balloon_inhibit()
  target/i386: sev: Use ram_block_discard_disable()
  migration/rdma: Use ram_block_discard_disable()
  migration/colo: Use ram_block_discard_disable()
  linux-headers: update to contain virtio-mem
  virtio-mem: Paravirtualized memory hot(un)plug
  virtio-pci: Proxy for virtio-mem
  MAINTAINERS: Add myself as virtio-mem maintainer
  hmp: Handle virtio-mem when printing memory device info
  numa: Handle virtio-mem in NUMA stats
  pc: Support for virtio-mem-pci
  virtio-mem: Allow notifiers for size changes
  virtio-pci: Send qapi events when the virtio-mem size changes
  virtio-mem: Migration sanity checks
  virtio-mem: Add trace events

 MAINTAINERS                                 |   8 +
 accel/kvm/kvm-all.c                         |   3 +-
 balloon.c                                   |  17 -
 exec.c                                      |  52 ++
 hw/core/numa.c                              |   6 +
 hw/i386/Kconfig                             |   1 +
 hw/i386/pc.c                                |  49 +-
 hw/s390x/s390-virtio-ccw.c                  |  22 +-
 hw/vfio/ap.c                                |  10 +-
 hw/vfio/ccw.c                               |  11 +-
 hw/vfio/common.c                            |  53 +-
 hw/vfio/pci.c                               |   6 +-
 hw/virtio/Kconfig                           |  11 +
 hw/virtio/Makefile.objs                     |   2 +
 hw/virtio/trace-events                      |  10 +
 hw/virtio/virtio-balloon.c                  |   8 +-
 hw/virtio/virtio-mem-pci.c                  | 157 ++++
 hw/virtio/virtio-mem-pci.h                  |  34 +
 hw/virtio/virtio-mem.c                      | 816 ++++++++++++++++++++
 include/exec/memory.h                       |  41 +
 include/hw/pci/pci.h                        |   1 +
 include/hw/vfio/vfio-common.h               |   4 +-
 include/hw/virtio/virtio-mem.h              |  83 ++
 include/migration/colo.h                    |   2 +-
 include/migration/misc.h                    |   2 +
 include/standard-headers/linux/virtio_ids.h |   1 +
 include/standard-headers/linux/virtio_mem.h | 211 +++++
 include/sysemu/balloon.h                    |   2 -
 migration/migration.c                       |  15 +-
 migration/postcopy-ram.c                    |  23 -
 migration/rdma.c                            |  18 +-
 migration/savevm.c                          |  11 +-
 monitor/hmp-cmds.c                          |  16 +
 monitor/monitor.c                           |   1 +
 qapi/misc.json                              |  64 +-
 target/i386/sev.c                           |   1 +
 36 files changed, 1651 insertions(+), 121 deletions(-)
 create mode 100644 hw/virtio/virtio-mem-pci.c
 create mode 100644 hw/virtio/virtio-mem-pci.h
 create mode 100644 hw/virtio/virtio-mem.c
 create mode 100644 include/hw/virtio/virtio-mem.h
 create mode 100644 include/standard-headers/linux/virtio_mem.h
```
#### [PATCH 0/2] Fix breakage from adding MSR_IA32_UMWAIT_CONTROL
##### From: Maxim Levitsky <mlevitsk@redhat.com>

```c
From patchwork Wed May 20 16:07:38 2020
Content-Type: text/plain; charset="utf-8"
MIME-Version: 1.0
Content-Transfer-Encoding: 7bit
X-Patchwork-Submitter: Maxim Levitsky <mlevitsk@redhat.com>
X-Patchwork-Id: 11560653
Return-Path: <SRS0=QMHo=7C=vger.kernel.org=kvm-owner@kernel.org>
Received: from mail.kernel.org (pdx-korg-mail-1.web.codeaurora.org
 [172.30.200.123])
	by pdx-korg-patchwork-2.web.codeaurora.org (Postfix) with ESMTP id 134DD14C0
	for <patchwork-kvm@patchwork.kernel.org>;
 Wed, 20 May 2020 16:07:49 +0000 (UTC)
Received: from vger.kernel.org (vger.kernel.org [23.128.96.18])
	by mail.kernel.org (Postfix) with ESMTP id E380C20759
	for <patchwork-kvm@patchwork.kernel.org>;
 Wed, 20 May 2020 16:07:48 +0000 (UTC)
Authentication-Results: mail.kernel.org;
	dkim=pass (1024-bit key) header.d=redhat.com header.i=@redhat.com
 header.b="K+rWIHfy"
Received: (majordomo@vger.kernel.org) by vger.kernel.org via listexpand
        id S1726560AbgETQHs (ORCPT
        <rfc822;patchwork-kvm@patchwork.kernel.org>);
        Wed, 20 May 2020 12:07:48 -0400
Received: from us-smtp-delivery-1.mimecast.com ([207.211.31.120]:47860 "EHLO
        us-smtp-1.mimecast.com" rhost-flags-OK-OK-OK-FAIL) by vger.kernel.org
        with ESMTP id S1726525AbgETQHr (ORCPT <rfc822;kvm@vger.kernel.org>);
        Wed, 20 May 2020 12:07:47 -0400
DKIM-Signature: v=1; a=rsa-sha256; c=relaxed/relaxed; d=redhat.com;
        s=mimecast20190719; t=1589990867;
        h=from:from:reply-to:subject:subject:date:date:message-id:message-id:
         to:to:cc:cc:mime-version:mime-version:content-type:content-type:
         content-transfer-encoding:content-transfer-encoding;
        bh=fQKGsMsjEI47tjZWYdHWX5AT4E07YQAP5+tDCquOeU0=;
        b=K+rWIHfy97IbOYiH674N6z47ZVaCtaMYr/RkJZdZxzzuV1tfs82o5KcLWpDKW082rIqfFj
        8APmcywB9s8ikb7X+84M7NfuIYlT2Kx48ss54+Wl7b4TU0MzrSjGucxZbq4hxaxiZjIy9L
        bSINhURGcu0Ubk+lW7woND3axEch6jg=
Received: from mimecast-mx01.redhat.com (mimecast-mx01.redhat.com
 [209.132.183.4]) (Using TLS) by relay.mimecast.com with ESMTP id
 us-mta-387-IuMYnOiOPFKkgir_dK6JXQ-1; Wed, 20 May 2020 12:07:45 -0400
X-MC-Unique: IuMYnOiOPFKkgir_dK6JXQ-1
Received: from smtp.corp.redhat.com (int-mx08.intmail.prod.int.phx2.redhat.com
 [10.5.11.23])
        (using TLSv1.2 with cipher AECDH-AES256-SHA (256/256 bits))
        (No client certificate requested)
        by mimecast-mx01.redhat.com (Postfix) with ESMTPS id 2E9DB835B44;
        Wed, 20 May 2020 16:07:44 +0000 (UTC)
Received: from starship.fedora32vm (unknown [10.35.207.28])
        by smtp.corp.redhat.com (Postfix) with ESMTP id 23F1D341FD;
        Wed, 20 May 2020 16:07:41 +0000 (UTC)
From: Maxim Levitsky <mlevitsk@redhat.com>
To: kvm@vger.kernel.org
Cc: linux-kernel@vger.kernel.org, Maxim Levitsky <mlevitsk@redhat.com>
Subject: [PATCH 0/2] Fix breakage from adding MSR_IA32_UMWAIT_CONTROL
Date: Wed, 20 May 2020 19:07:38 +0300
Message-Id: <20200520160740.6144-1-mlevitsk@redhat.com>
MIME-Version: 1.0
X-Scanned-By: MIMEDefang 2.84 on 10.5.11.23
Sender: kvm-owner@vger.kernel.org
Precedence: bulk
List-ID: <kvm.vger.kernel.org>
X-Mailing-List: kvm@vger.kernel.org

Currently code in kvm_get_supported_msrs always
returns this msr as supported by KVM, however it
is not supported at all on AMD and it is only supported
on few select Intel systems.

This happened to work in native virtualization case,
because KVM's code also tries to read these msrs,
and on an exception drops them from the supported msr list.

However when running nested, and outer hypervisor set
to ignore unknown msrs, the read from this msr doesn't get
an excption, and thus KVM thinks that this msr should be on
supported msr list.

I don't think we should rely on exception to check if a feature
is supported since that msr can be even in theory assigned to something
else on AMD for example.

Also I included a cosmetic fix for an issue I found in the same function.

Best regards,
	Maxim Levitsky

Maxim Levitsky (2):
  kvm: cosmetic: remove wrong braces in kvm_init_msr_list switch
  kvm/x86: don't expose MSR_IA32_UMWAIT_CONTROL unconditionally

 arch/x86/kvm/x86.c | 7 +++++--
 1 file changed, 5 insertions(+), 2 deletions(-)
```
#### [PATCH 0/1] RFC: Make thunderbolt NHI driver work with kexec
##### From: Maxim Levitsky <mlevitsk@redhat.com>

```c
From patchwork Wed May 20 18:12:39 2020
Content-Type: text/plain; charset="utf-8"
MIME-Version: 1.0
Content-Transfer-Encoding: 7bit
X-Patchwork-Submitter: Maxim Levitsky <mlevitsk@redhat.com>
X-Patchwork-Id: 11560979
Return-Path: <SRS0=QMHo=7C=vger.kernel.org=kvm-owner@kernel.org>
Received: from mail.kernel.org (pdx-korg-mail-1.web.codeaurora.org
 [172.30.200.123])
	by pdx-korg-patchwork-2.web.codeaurora.org (Postfix) with ESMTP id 380AF912
	for <patchwork-kvm@patchwork.kernel.org>;
 Wed, 20 May 2020 18:12:50 +0000 (UTC)
Received: from vger.kernel.org (vger.kernel.org [23.128.96.18])
	by mail.kernel.org (Postfix) with ESMTP id 209FA207D4
	for <patchwork-kvm@patchwork.kernel.org>;
 Wed, 20 May 2020 18:12:50 +0000 (UTC)
Authentication-Results: mail.kernel.org;
	dkim=pass (1024-bit key) header.d=redhat.com header.i=@redhat.com
 header.b="B4bC87k9"
Received: (majordomo@vger.kernel.org) by vger.kernel.org via listexpand
        id S1726830AbgETSMt (ORCPT
        <rfc822;patchwork-kvm@patchwork.kernel.org>);
        Wed, 20 May 2020 14:12:49 -0400
Received: from us-smtp-1.mimecast.com ([205.139.110.61]:32298 "EHLO
        us-smtp-delivery-1.mimecast.com" rhost-flags-OK-OK-OK-FAIL)
        by vger.kernel.org with ESMTP id S1726510AbgETSMs (ORCPT
        <rfc822;kvm@vger.kernel.org>); Wed, 20 May 2020 14:12:48 -0400
DKIM-Signature: v=1; a=rsa-sha256; c=relaxed/relaxed; d=redhat.com;
        s=mimecast20190719; t=1589998367;
        h=from:from:reply-to:subject:subject:date:date:message-id:message-id:
         to:to:cc:cc:mime-version:mime-version:content-type:content-type:
         content-transfer-encoding:content-transfer-encoding;
        bh=zrw5y7kLFs3H7gZeyqDRl7rn5ygDIp8+n8h/0YJPWVI=;
        b=B4bC87k9pmNYo0t09OHoGcbRWl3/v9ZoidSc5OpufmRCKfnseMla3PPpqlvbxuCbKFdSav
        mE6gNgI1sYTTY25nux2Wu9N/urK9E1k4dVJTpQbZMQbjZP3pJ3Cgq+962Ohv2xBc8cSGGE
        aqtk10XizqCBqOt4DgGwpoAqAimcE1o=
Received: from mimecast-mx01.redhat.com (mimecast-mx01.redhat.com
 [209.132.183.4]) (Using TLS) by relay.mimecast.com with ESMTP id
 us-mta-376-CrulUyWvP3-cx0zX3tnSag-1; Wed, 20 May 2020 14:12:45 -0400
X-MC-Unique: CrulUyWvP3-cx0zX3tnSag-1
Received: from smtp.corp.redhat.com (int-mx07.intmail.prod.int.phx2.redhat.com
 [10.5.11.22])
        (using TLSv1.2 with cipher AECDH-AES256-SHA (256/256 bits))
        (No client certificate requested)
        by mimecast-mx01.redhat.com (Postfix) with ESMTPS id 01DD11800D42;
        Wed, 20 May 2020 18:12:44 +0000 (UTC)
Received: from starship.redhat.com (unknown [10.35.207.28])
        by smtp.corp.redhat.com (Postfix) with ESMTP id A2C2010640E1;
        Wed, 20 May 2020 18:12:41 +0000 (UTC)
From: Maxim Levitsky <mlevitsk@redhat.com>
To: kvm@vger.kernel.org
Cc: Michael Jamet <michael.jamet@intel.com>,
        Mika Westerberg <mika.westerberg@linux.intel.com>,
        Andreas Noever <andreas.noever@gmail.com>,
        linux-usb@vger.kernel.org (open list:THUNDERBOLT DRIVER),
        Yehezkel Bernat <YehezkelShB@gmail.com>,
        linux-kernel@vger.kernel.org (open list),
        Jiri Kosina <trivial@kernel.org>,
        Maxim Levitsky <mlevitsk@redhat.com>
Subject: [PATCH 0/1] RFC: Make thunderbolt NHI driver work with kexec
Date: Wed, 20 May 2020 21:12:39 +0300
Message-Id: <20200520181240.118559-1-mlevitsk@redhat.com>
MIME-Version: 1.0
X-Scanned-By: MIMEDefang 2.84 on 10.5.11.22
Sender: kvm-owner@vger.kernel.org
Precedence: bulk
List-ID: <kvm.vger.kernel.org>
X-Mailing-List: kvm@vger.kernel.org

While trying to use kexec on my desktop,
which has Titan Ridge Thunderbolt add-on card,
I noticed that after a kexec, system hangs
for about a minute in the 'wating for udev devices
to settle' phase of the boot.

I found out that if I unload the thunderbolt driver
prior to the kexec, then it works just fine.

Looking at the code I see that the driver doeesn't
implement .shutdown method, so this on-liner patch
points it to the same method that is used on device removal.

I don't know if this is the right solution,
but it does appear to work just fine with this patch.

What do you think?

Best regards,
	Maxim Levitsky

Maxim Levitsky (1):
  thunderbolt: add trivial .shutdown

 drivers/thunderbolt/nhi.c | 1 +
 1 file changed, 1 insertion(+)
```
#### [PATCH Kernel v23 0/8] Add UAPIs to support migration for VFIO
##### From: Kirti Wankhede <kwankhede@nvidia.com>

```c
From patchwork Wed May 20 18:08:00 2020
Content-Type: text/plain; charset="utf-8"
MIME-Version: 1.0
Content-Transfer-Encoding: 7bit
X-Patchwork-Submitter: Kirti Wankhede <kwankhede@nvidia.com>
X-Patchwork-Id: 11561025
Return-Path: <SRS0=QMHo=7C=vger.kernel.org=kvm-owner@kernel.org>
Received: from mail.kernel.org (pdx-korg-mail-1.web.codeaurora.org
 [172.30.200.123])
	by pdx-korg-patchwork-2.web.codeaurora.org (Postfix) with ESMTP id 6602414B7
	for <patchwork-kvm@patchwork.kernel.org>;
 Wed, 20 May 2020 18:41:21 +0000 (UTC)
Received: from vger.kernel.org (vger.kernel.org [23.128.96.18])
	by mail.kernel.org (Postfix) with ESMTP id 48C32207ED
	for <patchwork-kvm@patchwork.kernel.org>;
 Wed, 20 May 2020 18:41:21 +0000 (UTC)
Authentication-Results: mail.kernel.org;
	dkim=pass (2048-bit key) header.d=nvidia.com header.i=@nvidia.com
 header.b="Ixg93cC+"
Received: (majordomo@vger.kernel.org) by vger.kernel.org via listexpand
        id S1726826AbgETSlU (ORCPT
        <rfc822;patchwork-kvm@patchwork.kernel.org>);
        Wed, 20 May 2020 14:41:20 -0400
Received: from hqnvemgate25.nvidia.com ([216.228.121.64]:13557 "EHLO
        hqnvemgate25.nvidia.com" rhost-flags-OK-OK-OK-OK) by vger.kernel.org
        with ESMTP id S1726548AbgETSlT (ORCPT <rfc822;kvm@vger.kernel.org>);
        Wed, 20 May 2020 14:41:19 -0400
Received: from hqpgpgate102.nvidia.com (Not Verified[216.228.121.13]) by
 hqnvemgate25.nvidia.com (using TLS: TLSv1.2, DES-CBC3-SHA)
        id <B5ec579800000>; Wed, 20 May 2020 11:40:00 -0700
Received: from hqmail.nvidia.com ([172.20.161.6])
  by hqpgpgate102.nvidia.com (PGP Universal service);
  Wed, 20 May 2020 11:41:19 -0700
X-PGP-Universal: processed;
        by hqpgpgate102.nvidia.com on Wed, 20 May 2020 11:41:19 -0700
Received: from HQMAIL105.nvidia.com (172.20.187.12) by HQMAIL109.nvidia.com
 (172.20.187.15) with Microsoft SMTP Server (TLS) id 15.0.1473.3; Wed, 20 May
 2020 18:41:19 +0000
Received: from kwankhede-dev.nvidia.com (10.124.1.5) by HQMAIL105.nvidia.com
 (172.20.187.12) with Microsoft SMTP Server (TLS) id 15.0.1473.3 via Frontend
 Transport; Wed, 20 May 2020 18:41:12 +0000
From: Kirti Wankhede <kwankhede@nvidia.com>
To: <alex.williamson@redhat.com>, <cjia@nvidia.com>
CC: <kevin.tian@intel.com>, <ziye.yang@intel.com>,
        <changpeng.liu@intel.com>, <yi.l.liu@intel.com>,
        <mlevitsk@redhat.com>, <eskultet@redhat.com>, <cohuck@redhat.com>,
        <dgilbert@redhat.com>, <jonathan.davies@nutanix.com>,
        <eauger@redhat.com>, <aik@ozlabs.ru>, <pasic@linux.ibm.com>,
        <felipe@nutanix.com>, <Zhengxiao.zx@Alibaba-inc.com>,
        <shuangtai.tst@alibaba-inc.com>, <Ken.Xue@amd.com>,
        <zhi.a.wang@intel.com>, <yan.y.zhao@intel.com>,
        <qemu-devel@nongnu.org>, <kvm@vger.kernel.org>,
        "Kirti Wankhede" <kwankhede@nvidia.com>
Subject: [PATCH Kernel v23 0/8] Add UAPIs to support migration for VFIO
 devices
Date: Wed, 20 May 2020 23:38:00 +0530
Message-ID: <1589998088-3250-1-git-send-email-kwankhede@nvidia.com>
X-Mailer: git-send-email 2.7.0
X-NVConfidentiality: public
MIME-Version: 1.0
DKIM-Signature: v=1; a=rsa-sha256; c=relaxed/relaxed; d=nvidia.com; s=n1;
        t=1590000000; bh=AM3IVEduZ9Pu1HzFHKiWqLkCNZyHWg2iLI5sdDAJEvU=;
        h=X-PGP-Universal:From:To:CC:Subject:Date:Message-ID:X-Mailer:
         X-NVConfidentiality:MIME-Version:Content-Type;
        b=Ixg93cC+dk969QKdj9nRaEaYdzT1IeCngIBEb21tOcNtjygLMK6RJNapRR9UpoIqm
         zih33Pd5CS88neUYLx6jIajWdxBTV9Cy3X5qHB2yjJKVTK3+aW3NoIx6GAUe0g2Yq/
         Fwxly15hRbaBGmi0Obam2+xiKM+ADoQD1JBMKBFhkDKD9QqcI8e+8wz1hecXWdSxYY
         7BJDSgyFV6JAh/Mvvi3SOA3U2ky1Dy05Lb0Y+xs4X5MMbsDazs5wsJ14ZKEZfvbTnH
         KcW+3krAnlA5dULoQ56apHjnDuRp9UxymKkZy4XP3PJpEA4zKW8DPlOIATTyT/7anN
         +eUR8mdTVNMQw==
Sender: kvm-owner@vger.kernel.org
Precedence: bulk
List-ID: <kvm.vger.kernel.org>
X-Mailing-List: kvm@vger.kernel.org

Hi,

This patch set adds:
* IOCTL VFIO_IOMMU_DIRTY_PAGES to get dirty pages bitmap with
  respect to IOMMU container rather than per device. All pages pinned by
  vendor driver through vfio_pin_pages external API has to be marked as
  dirty during  migration. When IOMMU capable device is present in the
  container and all pages are pinned and mapped, then all pages are marked
  dirty.
  When there are CPU writes, CPU dirty page tracking can identify dirtied
  pages, but any page pinned by vendor driver can also be written by
  device. As of now there is no device which has hardware support for
  dirty page tracking. So all pages which are pinned should be considered
  as dirty.
  This ioctl is also used to start/stop dirty pages tracking for pinned and
  unpinned pages while migration is active.

* Updated IOCTL VFIO_IOMMU_UNMAP_DMA to get dirty pages bitmap before
  unmapping IO virtual address range.
  With vIOMMU, during pre-copy phase of migration, while CPUs are still
  running, IO virtual address unmap can happen while device still keeping
  reference of guest pfns. Those pages should be reported as dirty before
  unmap, so that VFIO user space application can copy content of those
  pages from source to destination.

* Patch 8 detect if IOMMU capable device driver is smart to report pages
  to be marked dirty by pinning pages using vfio_pin_pages() API.


Yet TODO:
Since there is no device which has hardware support for system memmory
dirty bitmap tracking, right now there is no other API from vendor driver
to VFIO IOMMU module to report dirty pages. In future, when such hardware
support will be implemented, an API will be required such that vendor
driver could report dirty pages to VFIO module during migration phases.

v22 -> v23
- Fixed issue reported by Yan
https://lore.kernel.org/kvm/97977ede-3c5b-c5a5-7858-7eecd7dd531c@nvidia.com/
- Fixed nit picks suggested by Cornelia

v21 -> v22
- Fixed issue raised by Alex :
https://lore.kernel.org/kvm/20200515163307.72951dd2@w520.home/

v20 -> v21
- Added checkin for GET_BITMAP ioctl for vfio_dma boundaries.
- Updated unmap ioctl function - as suggested by Alex.
- Updated comments in DIRTY_TRACKING ioctl definition - as suggested by
  Cornelia.

v19 -> v20
- Fixed ioctl to get dirty bitmap to get bitmap of multiple vfio_dmas
- Fixed unmap ioctl to get dirty bitmap of multiple vfio_dmas.
- Removed flag definition from migration capability.

v18 -> v19
- Updated migration capability with supported page sizes bitmap for dirty
  page tracking and  maximum bitmap size supported by kernel module.
- Added patch to calculate and cache pgsize_bitmap when iommu->domain_list
  is updated.
- Removed extra buffers added in previous version for bitmap manipulation
  and optimised the code.

v17 -> v18
- Add migration capability to the capability chain for VFIO_IOMMU_GET_INFO
  ioctl
- Updated UMAP_DMA ioctl to return bitmap of multiple vfio_dma

v16 -> v17
- Fixed errors reported by kbuild test robot <lkp@intel.com> on i386

v15 -> v16
- Minor edits and nit picks (Auger Eric)
- On copying bitmap to user, re-populated bitmap only for pinned pages,
  excluding unmapped pages and CPU dirtied pages.
- Patches are on tag: next-20200318 and 1-3 patches from Yan's series
  https://lkml.org/lkml/2020/3/12/1255

v14 -> v15
- Minor edits and nit picks.
- In the verification of user allocated bitmap memory, added check of
   maximum size.
- Patches are on tag: next-20200318 and 1-3 patches from Yan's series
  https://lkml.org/lkml/2020/3/12/1255

v13 -> v14
- Added struct vfio_bitmap to kabi. updated structure
  vfio_iommu_type1_dirty_bitmap_get and vfio_iommu_type1_dma_unmap.
- All small changes suggested by Alex.
- Patches are on tag: next-20200318 and 1-3 patches from Yan's series
  https://lkml.org/lkml/2020/3/12/1255

v12 -> v13
- Changed bitmap allocation in vfio_iommu_type1 to per vfio_dma
- Changed VFIO_IOMMU_DIRTY_PAGES ioctl behaviour to be per vfio_dma range.
- Changed vfio_iommu_type1_dirty_bitmap structure to have separate data
  field.

v11 -> v12
- Changed bitmap allocation in vfio_iommu_type1.
- Remove atomicity of ref_count.
- Updated comments for migration device state structure about error
  reporting.
- Nit picks from v11 reviews

v10 -> v11
- Fix pin pages API to free vpfn if it is marked as unpinned tracking page.
- Added proposal to detect if IOMMU capable device calls external pin pages
  API to mark pages dirty.
- Nit picks from v10 reviews

v9 -> v10:
- Updated existing VFIO_IOMMU_UNMAP_DMA ioctl to get dirty pages bitmap
  during unmap while migration is active
- Added flag in VFIO_IOMMU_GET_INFO to indicate driver support dirty page
  tracking.
- If iommu_mapped, mark all pages dirty.
- Added unpinned pages tracking while migration is active.
- Updated comments for migration device state structure with bit
  combination table and state transition details.

v8 -> v9:
- Split patch set in 2 sets, Kernel and QEMU.
- Dirty pages bitmap is queried from IOMMU container rather than from
  vendor driver for per device. Added 2 ioctls to achieve this.

v7 -> v8:
- Updated comments for KABI
- Added BAR address validation check during PCI device's config space load
  as suggested by Dr. David Alan Gilbert.
- Changed vfio_migration_set_state() to set or clear device state flags.
- Some nit fixes.

v6 -> v7:
- Fix build failures.

v5 -> v6:
- Fix build failure.

v4 -> v5:
- Added decriptive comment about the sequence of access of members of
  structure vfio_device_migration_info to be followed based on Alex's
  suggestion
- Updated get dirty pages sequence.
- As per Cornelia Huck's suggestion, added callbacks to VFIODeviceOps to
  get_object, save_config and load_config.
- Fixed multiple nit picks.
- Tested live migration with multiple vfio device assigned to a VM.

v3 -> v4:
- Added one more bit for _RESUMING flag to be set explicitly.
- data_offset field is read-only for user space application.
- data_size is read for every iteration before reading data from migration,
  that is removed assumption that data will be till end of migration
  region.
- If vendor driver supports mappable sparsed region, map those region
  during setup state of save/load, similarly unmap those from cleanup
  routines.
- Handles race condition that causes data corruption in migration region
  during save device state by adding mutex and serialiaing save_buffer and
  get_dirty_pages routines.
- Skip called get_dirty_pages routine for mapped MMIO region of device.
- Added trace events.
- Split into multiple functional patches.

v2 -> v3:
- Removed enum of VFIO device states. Defined VFIO device state with 2
  bits.
- Re-structured vfio_device_migration_info to keep it minimal and defined
  action on read and write access on its members.

v1 -> v2:
- Defined MIGRATION region type and sub-type which should be used with
  region type capability.
- Re-structured vfio_device_migration_info. This structure will be placed
  at 0th offset of migration region.
- Replaced ioctl with read/write for trapped part of migration region.
- Added both type of access support, trapped or mmapped, for data section
  of the region.
- Moved PCI device functions to pci file.
- Added iteration to get dirty page bitmap until bitmap for all requested
  pages are copied.

Thanks,
Kirti


Kirti Wankhede (8):
  vfio: UAPI for migration interface for device state
  vfio iommu: Remove atomicity of ref_count of pinned pages
  vfio iommu: Cache pgsize_bitmap in struct vfio_iommu
  vfio iommu: Add ioctl definition for dirty pages tracking
  vfio iommu: Implementation of ioctl for dirty pages tracking
  vfio iommu: Update UNMAP_DMA ioctl to get dirty bitmap before unmap
  vfio iommu: Add migration capability to report supported features
  vfio: Selective dirty page tracking if IOMMU backed device pins pages

 drivers/vfio/vfio.c             |  13 +-
 drivers/vfio/vfio_iommu_type1.c | 571 ++++++++++++++++++++++++++++++++++++----
 include/linux/vfio.h            |   4 +-
 include/uapi/linux/vfio.h       | 318 ++++++++++++++++++++++
 4 files changed, 847 insertions(+), 59 deletions(-)
```