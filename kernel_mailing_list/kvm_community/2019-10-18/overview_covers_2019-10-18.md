

#### [PATCH v2 0/3] KVM: VMX: Some refactor of VMX vmcs and msr bitmap
##### From: Xiaoyao Li <xiaoyao.li@intel.com>

```c
From patchwork Fri Oct 18 09:37:20 2019
Content-Type: text/plain; charset="utf-8"
MIME-Version: 1.0
Content-Transfer-Encoding: 7bit
X-Patchwork-Submitter: Xiaoyao Li <xiaoyao.li@intel.com>
X-Patchwork-Id: 11198033
Return-Path: <SRS0=/4Fr=YL=vger.kernel.org=kvm-owner@kernel.org>
Received: from mail.kernel.org (pdx-korg-mail-1.web.codeaurora.org
 [172.30.200.123])
	by pdx-korg-patchwork-2.web.codeaurora.org (Postfix) with ESMTP id 13C8A18A6
	for <patchwork-kvm@patchwork.kernel.org>;
 Fri, 18 Oct 2019 09:52:44 +0000 (UTC)
Received: from vger.kernel.org (vger.kernel.org [209.132.180.67])
	by mail.kernel.org (Postfix) with ESMTP id F1C86222BD
	for <patchwork-kvm@patchwork.kernel.org>;
 Fri, 18 Oct 2019 09:52:43 +0000 (UTC)
Received: (majordomo@vger.kernel.org) by vger.kernel.org via listexpand
        id S2633148AbfJRJwZ (ORCPT
        <rfc822;patchwork-kvm@patchwork.kernel.org>);
        Fri, 18 Oct 2019 05:52:25 -0400
Received: from mga17.intel.com ([192.55.52.151]:35375 "EHLO mga17.intel.com"
        rhost-flags-OK-OK-OK-OK) by vger.kernel.org with ESMTP
        id S2404657AbfJRJwZ (ORCPT <rfc822;kvm@vger.kernel.org>);
        Fri, 18 Oct 2019 05:52:25 -0400
X-Amp-Result: SKIPPED(no attachment in message)
X-Amp-File-Uploaded: False
Received: from fmsmga004.fm.intel.com ([10.253.24.48])
  by fmsmga107.fm.intel.com with ESMTP/TLS/DHE-RSA-AES256-GCM-SHA384;
 18 Oct 2019 02:52:24 -0700
X-ExtLoop1: 1
X-IronPort-AV: E=Sophos;i="5.67,311,1566889200";
   d="scan'208";a="221689663"
Received: from lxy-clx-4s.sh.intel.com ([10.239.43.57])
  by fmsmga004.fm.intel.com with ESMTP; 18 Oct 2019 02:52:23 -0700
From: Xiaoyao Li <xiaoyao.li@intel.com>
To: Paolo Bonzini <pbonzini@redhat.com>,
 =?utf-8?b?UmFkaW0gS3LEjW3DocWZ?= <rkrcmar@redhat.com>,
 Sean Christopherson <sean.j.christopherson@intel.com>,
 Vitaly Kuznetsov <vkuznets@redhat.com>, Jim Mattson <jmattson@google.com>,
 Joerg Roedel <joro@8bytes.org>
Cc: Xiaoyao Li <xiaoyao.li@intel.com>, kvm@vger.kernel.org,
        linux-kernel@vger.kernel.org
Subject: [PATCH v2 0/3] KVM: VMX: Some refactor of VMX vmcs and msr bitmap
Date: Fri, 18 Oct 2019 17:37:20 +0800
Message-Id: <20191018093723.102471-1-xiaoyao.li@intel.com>
X-Mailer: git-send-email 2.19.1
MIME-Version: 1.0
Sender: kvm-owner@vger.kernel.org
Precedence: bulk
List-ID: <kvm.vger.kernel.org>
X-Mailing-List: kvm@vger.kernel.org

Remove the vcpu creation refactor and FPU allocation cleanup from v1, since
I need more time to invest Sean's suggestion.

This series add one patch to move the vmcs reset from vcpu_reset, based on
Krish's suggestion.

Xiaoyao Li (3):
  KVM: VMX: Move vmcs related resetting out of vmx_vcpu_reset()
  KVM: VMX: Rename {vmx,nested_vmx}_vcpu_setup() and minor cleanup
  KVM: VMX: Some minor refactor of MSR bitmap

 arch/x86/kvm/vmx/nested.c |   2 +-
 arch/x86/kvm/vmx/nested.h |   2 +-
 arch/x86/kvm/vmx/vmx.c    | 173 ++++++++++++++++++++------------------
 3 files changed, 91 insertions(+), 86 deletions(-)
```
