# Leetcode---1751
Maximum Number of Events That Can Be Attended II
//code in java 
import java.util.Arrays;

class Solution {
    public int maxValue(int[][] events, int k) {
        // Sort events by their end times to facilitate dynamic programming
        Arrays.sort(events, (a, b) -> a[1] - b[1]);

        int n = events.length;
        // dp[i][j] represents the maximum value achievable by attending at most 'j' events
        // considering events up to index 'i-1' (inclusive) after sorting.
        int[][] dp = new int[n + 1][k + 1];

        for (int i = 1; i <= n; ++i) {
            int currentStartTime = events[i - 1][0];
            int currentValue = events[i - 1][2];

            // Find the index of the latest event that finishes before the current event starts.
            // This uses binary search on the sorted 'events' array based on end times.
            int prevEventIndex = search(events, currentStartTime, i - 1);

            for (int j = 1; j <= k; ++j) {
                // Option 1: Don't attend the current event.
                // The maximum value is the same as considering events up to i-1 with 'j' events.
                int notAttending = dp[i - 1][j];

                // Option 2: Attend the current event.
                // Add the current event's value to the maximum value achievable
                // from attending 'j-1' events before the current event's start time.
                int attending = dp[prevEventIndex][j - 1] + currentValue;

                // Take the maximum of the two options.
                dp[i][j] = Math.max(notAttending, attending);
            }
        }
        return dp[n][k];
    }

    // Helper function to perform binary search for the latest non-overlapping event.
    // Finds the index 'p' such that events[p-1][1] < target (currentStartTime)
    // and events[p][1] >= target.
    private int search(int[][] events, int target, int high) {
        int low = 0;
        int result = 0; // Default to 0 if no such event is found

        while (low <= high) {
            int mid = low + (high - low) / 2;
            if (events[mid][1] < target) {
                result = mid + 1; // Possible candidate, try for a later event
                low = mid + 1;
            } else {
                high = mid - 1; // Current event overlaps, look earlier
            }
        }
        return result;
    }
}
