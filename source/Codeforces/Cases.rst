`Cases <https://codeforces.com/contest/1995/problem/D>`_
=============================================================

    这道题的思路十分巧妙。

    考虑到 ``c`` 很小，可行解的枚举范围并不大，因此我们完全可以通过枚举所有可行解来找到最小的答案。

    那么，如何判断一个方案是否为可行解呢？

    首先，我们需要知道，对于该字符串中的任意一个连续的、长度为 ``k`` 的子串，必须至少包含一个 ``cases`` ，且结尾必定是 ``cases`` 。

    如果上述所有情况中，至少有一个字符属于我们的方案，那么这个方案就是可行解。

    为了快速判断某个区间内是否存在某个字母，可以通过构造 ``c`` 个前缀和数组来解决这个问题。

    对于任意的长度为 ``k`` 的连续区间，可以用整型的不同二进制位来表示这个区间包含哪些字母，并用数组来存储这种情况是否存在，特别是要单独处理结尾部分。

    我们可以通过判断一个方案是否非法来确认其合法性。

    如何判断一个方案是否非法呢？

    自然是找出是否存在一种情况，其包含的字母都不在方案内。

    我们可以先通过寻找一种特殊的情况来对一部分方案进行断言。这种情况就是该方案的补集。

    如果这种补集存在，意味着该方案一定非法，那么该方案的子集也必定非法。

    基于这个思路，我们可以使用动态规划来转移这种状态。

    显然，这种方法不会错判，但可能会令人疑惑：会不会漏判呢？

    接下来我们探讨一下是否会漏判。

    对于某个被断言的非法方案，它肯定包含所有的不满足其补集情况的非法方案。

    由于我们枚举了所有可能的方案，那么任意非法方案一定不满足至少 ``1`` 个情况。

    因为被断言方案集合包含所有恰好不满足某种情况的非法方案。

    因此，所有非法方案一定是某个被断言方案的子集。
        
    .. code-block:: CPP

        #include <bits/stdc++.h>

        void solve() {
            int n, c, k;
            std::cin >> n >> c >> k;
            std::string s;
            std::cin >> s;

            std::vector<std::vector<int>> cnt(n + 1, std::vector<int>(c, 0));

            for (int i = 0; i < n; i++) {
                for (int j = 0; j < c; j++) {
                    cnt[i + 1][j] = cnt[i][j] + ((s[i] - 'A') == j);
                }
            }

            std::vector<bool> exist(1 << c, false);

            for (int i = 1; i <= n - k + 1; i++) {
                int mask = 0;
                for (int j = 0; j < c; j++) {
                    if (cnt[i + k - 1][j] > cnt[i - 1][j]) {
                        mask |= (1 << j);
                    }
                }
                exist[mask] = true;
            }

            exist[1 << (s.back() - 'A')] = true;

            std::vector<int> bad(1 << c);

            for (int i = 0; i < (1 << c); i++) {
                bad[i] = exist[((1 << c) - 1) ^ i];
            }

            for (int i = (1 << c) - 1; i >= 0; i--) {
                for (int j = 0; j < c; j++) {
                    bad[i] |= bad[i | (1 << j)];
                }
            }

            int res = c;

            for (int i = 0; i < (1 << c); i++) {
                if (!bad[i]) res = std::min(res, __builtin_popcount(i));
            }

            std::cout << res << '\n';
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