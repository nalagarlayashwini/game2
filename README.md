# game2
high school i/o project
import java.util.Scanner;

public class DailyHealthAdventure {

    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        HealthTracker player = new HealthTracker();

        int totalDays = 3; // A 3-day habit streak challenge

        System.out.println("====================================================");
        System.out.println("       3-DAY HABIT STREAK & COMPOUNDING CHALLENGE     ");
        System.out.println("====================================================");
        System.out.println("Guide your character through 3 full days. Good or bad choices");
        System.out.println("compound over time into streaks, fatigue, and sleep debt!\n");

        for (int day = 1; day <= totalDays; day++) {
            if (!player.isAlive()) {
                System.out.println("\nYour health gave out on Day " + day + ". Game Over.");
                break;
            }

            System.out.println("\n====================================================");
            System.out.println("                    --- DAY " + day + " ---                 ");
            System.out.println("====================================================");
            
            // Start a new day with overnight recovery / sleep debt check
            player.startNewDay();

            // SCENE 1: MORNING ROUTINE
            playScene(scanner, player,
                "7:30 AM - You just woke up! You're running a little behind schedule.",
                new Choice("Brush teeth, wash face, and eat a bowl of oatmeal", +20, +20, +15, true,
                    "Great start! Clean teeth and slow-burning carbs fuel your morning."),
                new Choice("Skip brushing teeth and grab a sugary pastry on the way out", -15, -10, +10, false,
                    "Quick energy, but sugar coats unbrushed teeth and causes a crash later."),
                new Choice("Hit snooze, skip breakfast, and rush out the door", -5, -15, -10, false,
                    "Extra sleep minutes gained, but your stomach is growling.")
            );

            // SCENE 2: BEFORE LUNCH
            playScene(scanner, player,
                "12:15 PM - Lunchtime! You just finished using shared computers and handling doorknobs.",
                new Choice("Wash hands with soap for 20 seconds, then eat a balanced lunch", +20, +20, +15, true,
                    "Smart move! Washing away germs protects your immune system before eating."),
                new Choice("Use a quick splash of water and eat fast food fries and soda", 0, -15, -5, false,
                    "Water misses germs, and heavy grease makes you sluggish."),
                new Choice("Start eating a healthy salad immediately without washing hands", -20, +15, +10, false,
                    "Great food choice, but unwashed hands transfer germs straight to your meal!")
            );

            // SCENE 3: AFTERNOON SLUMP
            playScene(scanner, player,
                "3:30 PM - Afternoon break! You feel your focus starting to fade.",
                new Choice("Drink a tall glass of water and take a 15-minute brisk walk", +5, +10, +20, true,
                    "Hydration and movement boost blood flow to your brain naturally."),
                new Choice("Drink an energy drink and scroll on your phone", 0, -15, +5, false,
                    "Temporary caffeine boost, but high sugar and screen strain catch up."),
                new Choice("Eat an apple with peanut butter", +5, +20, +15, true,
                    "Fiber and healthy fats stabilize your afternoon energy!")
            );

            // SCENE 4: BEDTIME ROUTINE
            playScene(scanner, player,
                "9:30 PM - Time for bed! You're tired from a long day.",
                new Choice("Brush & floss teeth, wash hands/face, and sleep 8 hours", +25, +5, +25, true,
                    "Text-book bedtime routine! Your body and teeth recover overnight."),
                new Choice("Fall straight into bed in your daytime clothes without brushing", -25, 0, +10, false,
                    "Bacteria build up overnight on your teeth and skin."),
                new Choice("Brush teeth, then stay up until 1:00 AM gaming", +15, 0, -25, false,
                    "Clean teeth, but losing sleep weakens your immune system for tomorrow.")
            );

            // End of day recap
            player.printDayRecap(day);
        }

        player.printFinalWeeklyReport();
        scanner.close();
    }

    private static void playScene(Scanner scanner, HealthTracker player, String scenario,
                                  Choice c1, Choice c2, Choice c3) {
        Choice[] choices = {c1, c2, c3};

        System.out.println("\n----------------------------------------------------");
        System.out.println(scenario);
        System.out.println("----------------------------------------------------");
        for (int i = 0; i < choices.length; i++) {
            System.out.println((i + 1) + ". " + choices[i].text);
        }

        int pick = -1;
        while (pick < 1 || pick > 3) {
            System.out.print("Your decision (1-3): ");
            if (scanner.hasNextInt()) {
                pick = scanner.nextInt();
            } else {
                scanner.next();
            }
        }

        Choice chosen = choices[pick - 1];
        player.applyChoice(chosen, chosen.isHealthy);
        System.out.println("\n>> " + chosen.feedback);
        player.printCurrentStatus();
    }
}

class HealthTracker {
    private int hygiene = 70;
    private int nutrition = 70;
    private int energy = 70;
    
    // Multi-day progression trackers
    private int healthyStreak = 0;
    private int sleepDebt = 0;

    public void applyChoice(Choice c, boolean isHealthyChoice) {
        hygiene = clamp(hygiene + c.hygieneDelta);
        nutrition = clamp(nutrition + c.nutritionDelta);
        energy = clamp(energy + c.energyDelta);

        if (isHealthyChoice) {
            healthyStreak++;
            System.out.println("🔥 Streak Bonus! Healthy habit streak is now: " + healthyStreak);
        } else {
            healthyStreak = 0;
            System.out.println("⚠️ Habit broken! Streak reset to 0.");
        }
    }

    public void startNewDay() {
        System.out.println("☀️ Morning recovery! Body resets, checking previous day's fatigue.");
        
        if (energy < 40) {
            sleepDebt += 20;
            energy = Math.max(20, 60 - sleepDebt);
            System.out.println("💤 You woke up tired because of poor sleep or low energy yesterday. Sleep debt compounds!");
        } else {
            sleepDebt = Math.max(0, sleepDebt - 10);
            energy = 80; // Fresh start
            System.out.println("⚡ Well-rested! You start the day with high baseline energy.");
        }
        
        hygiene = Math.max(40, hygiene - 10); // Natural overnight drop
        nutrition = Math.max(40, nutrition - 10);
    }

    public boolean isAlive() {
        return this.energy > 0;
    }

    private int clamp(int value) {
        return Math.max(0, Math.min(100, value));
    }

    public void printCurrentStatus() {
        System.out.println("[Stats] Hygiene: " + hygiene + "/100 | Nutrition: " + nutrition + "/100 | Energy: " + energy + "/100");
    }

    public void printDayRecap(int day) {
        System.out.println("\n--- END OF DAY " + day + " SUMMARY ---");
        System.out.println("Hygiene: " + hygiene + " | Nutrition: " + nutrition + " | Energy: " + energy);
        System.out.println("Current Healthy Streak: " + healthyStreak);
    }

    public void printFinalWeeklyReport() {
        int overallScore = (hygiene + nutrition + energy) / 3;

        System.out.println("\n====================================================");
        System.out.println("          FINAL 3-DAY HEALTH & HABIT REPORT         ");
        System.out.println("====================================================");
        System.out.println("Max Healthy Streak Achieved: " + healthyStreak);
        System.out.println("Final Body Status -> Energy: " + energy + " | Hygiene: " + hygiene + " | Nutrition: " + nutrition);
        System.out.println("Overall Average Score: " + overallScore + " / 100");

        if (healthyStreak >= 8 && overallScore >= 75) {
            System.out.println("🏆 RANK S (Wellness Master) - Outstanding consistency and habits!");
        } else if (overallScore >= 60) {
            System.out.println("📈 RANK A (Balanced & Healthy) - Great job maintaining your routines!");
        } else {
            System.out.println("⚠️ RANK B (Burnout Risk) - Inconsistent habits caused compounding fatigue. Try again!");
        }
        System.out.println("====================================================");
    }
}

class Choice {
    String text;
    int hygieneDelta;
    int nutritionDelta;
    int energyDelta;
    boolean isHealthy;
    String feedback;

    public Choice(String text, int hygieneDelta, int nutritionDelta, int energyDelta, boolean isHealthy, String feedback) {
        this.text = text;
        this.hygieneDelta = hygieneDelta;
        this.nutritionDelta = nutritionDelta;
        this.energyDelta = energyDelta;
        this.isHealthy = isHealthy;
        this.feedback = feedback;
    }
}
