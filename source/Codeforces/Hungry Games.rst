`Hungry Games <https://codeforces.com/contest/1994/problem/C>`_
=====================================================================

    可以通过逆向思维将题意转化为求 ``g`` 最终值为 ``0`` 的方案数。

    我们发现，当从某个位置出发模拟题目操作时， ``g`` 为 ``0`` 时所在的位置十分特殊。

    以 ``g`` 为 ``0`` 的位置作为截断后会发现，固定任意起点，对于能到达的所有 ``g`` 为 ``0`` 位置的过程都可以看成 ``00`` 之间的跳跃。

    由于 ``0`` 点的特殊性，每一段都能独立开进行计算。假如你计算某个位置的方案数，自然就可以利用离该位置最近的那个 ``0`` 号位。

    那么我们可以倒着计算某个位置的方案数，从而借用 ``dp`` 的思想求出各位置答案。

    考虑设计 ``dp`` 状态， ``dp[x]`` 代表着跳到 ``x`` 位置时 ``0`` 出现的次数。很显然，如果某个位置由某个状态转移过来，它一定会先跳到一个 ``0`` 位置，因为它们的终止点统一为 ``0`` 。

    为什么 ``dp[n] = 1`` 呢？显然，当跳到 ``n`` 号位置时，此时的 ``g`` 必定为 ``0`` ，所以为 ``1`` 。

    .. code-block:: CPP

        while (s[j - 1] - s[i] > x) {
            j--;
        }      

    这一步是为了寻找位置 ``j`` 使得跳到该位置恰好为 ``0`` 。利用双指针思想能够线性的找出所需位置，当然，为了确保每一段的正确性，需要找最接近的 ``0``。

    ``dp[i] = 1 + (j <= n ? dp[j] : 0)`` 转移状态就很好理解了。

    但是 ``ans -= dp[i] - 1`` 为什么 ``-1`` 呢？

    在这里我们的状态并不是某个位置出发的方案数，而是跳到某个位置的方案数。

    因此我们外层循环下标从 ``0`` 开始，但是我们也可以很方便求下标从某个位置开始的方案数。

    假如得到跳到下标 ``x`` 的方案数，要求出下标从 ``x + 1`` 开始的方案数，我们只需要简单的减去 ``1`` 就可以了。

    因为跳到下标 ``x`` 的方案数包含了在下标 ``x`` 上的情况，这时 ``g`` 确实为 ``0`` 。但我们实际不需要加上这一次。因为我们会从下标 ``x + 1`` 开始。

    综上，减去这些过程中的方案数就能得到该题答案。

    .. code-block:: CPP

        #include <bits/stdc++.h>

        using i64 = long long;

        void solve() {
            int n, x;
            std::cin >> n >> x;

            std::vector<int> a(n);
            for (int i = 0; i < n; i++) {
                std::cin >> a[i];
            }

            std::vector<i64> s(n + 1);
            for (int i = 0; i < n; i++) {
                s[i + 1] = s[i] + a[i];
            }

            i64 ans = 1LL * n * (n + 1) / 2;
            std::vector<int> dp(n + 1);
            dp[n] = 1;
            for (int i = n - 1, j = n + 1; i >= 0; i--) {
                while (s[j - 1] - s[i] > x) {
                    j--;
                }
                dp[i] = 1 + (j <= n ? dp[j] : 0);
                ans -= dp[i] - 1;
            }
            std::cout << ans << "\n";
        }

        int main() {
            std::ios::sync_with_stdio(false);
            std::cin.tie(nullptr);

            int t;
            std::cin >> t;

            while (t--) {
                solve();
            }

            return 0;
        }        
